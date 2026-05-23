# Error Handling

## Philosophy

Every async operation has three states: **loading**, **data**, and **error**. All three must be represented in the UI. Silent failures are not acceptable.

## Domain Error Type

All HTTP errors are normalized to `ApiError` by the error interceptor before they reach components or stores.

```typescript
// core/http/api-error.ts
export class ApiError extends Error {
  constructor(
    public readonly status: number,
    message: string,
    public readonly fieldErrors?: Record<string, string>,
  ) {
    super(message);
    this.name = 'ApiError';
  }

  get isNotFound(): boolean { return this.status === 404; }
  get isUnauthorized(): boolean { return this.status === 401; }
  get isForbidden(): boolean { return this.status === 403; }
  get isServerError(): boolean { return this.status >= 500; }
  get isValidation(): boolean { return this.status === 422; }
}
```

## Global Angular Error Handler (Sentry)

```typescript
// core/observability/sentry-error-handler.ts
@Injectable()
export class SentryErrorHandler implements ErrorHandler {
  handleError(error: unknown): void {
    const err = error instanceof Error ? error : new Error(String(error));
    Sentry.captureException(err);
    console.error(err); // allowed in error handler only
  }
}

// app.config.ts
{ provide: ErrorHandler, useClass: SentryErrorHandler }
```

## Store Error Pattern

Every store service exposes loading, data, and error as separate read-only signals:

```typescript
@Injectable({ providedIn: 'root' })
export class ProspectStore {
  private readonly _loading = signal(false);
  private readonly _error = signal<string | null>(null);
  private readonly _prospects = signal<Prospect[]>([]);

  readonly loading = this._loading.asReadonly();
  readonly error = this._error.asReadonly();
  readonly prospects = this._prospects.asReadonly();

  load(): void {
    this._loading.set(true);
    this._error.set(null);
    this.service.getAll().subscribe({
      next: list => { this._prospects.set(list); this._loading.set(false); },
      error: (err: ApiError) => {
        this._error.set(err.message);
        this._loading.set(false);
        inject(SentryService).captureError(err, { operation: 'ProspectStore.load' });
      },
    });
  }
}
```

## Template Error Pattern

```html
@if (store.loading()) {
  <app-spinner aria-label="Loading prospects..." />
} @else if (store.error()) {
  <app-error-banner
    [message]="store.error()!"
    [retryLabel]="'Retry'"
    (retry)="store.load()"
  />
} @else if (store.isEmpty()) {
  <app-empty-state
    message="No prospects found."
    [actionLabel]="'Run Discovery'"
    (action)="runDiscovery()"
  />
} @else {
  @for (p of store.prospects(); track p.id) {
    <app-prospect-card [prospect]="p" />
  }
}
```

## Shared Error Components

Build these in `src/app/shared/components/`:

- `ErrorBannerComponent` — dismissable banner with optional retry button.
- `SpinnerComponent` — accessible loading indicator with `aria-live="polite"`.
- `EmptyStateComponent` — empty list state with optional CTA.
- `NotFoundComponent` — 404 page for unknown routes.

## Route-Level Error Handling

For resolver errors, provide a `resolve` error handler or use `Router.errorHandler`:

```typescript
// app.config.ts
provideRouter(routes,
  withComponentInputBinding(),
  withRouterConfig({ resolveNavigationPromiseOnError: true }),
)
```

If a resolver throws, redirect to an error page rather than leaving the user on a blank screen.
