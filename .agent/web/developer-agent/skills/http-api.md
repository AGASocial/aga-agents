# HTTP & API Layer

## Setup

```typescript
// app.config.ts
provideHttpClient(
  withInterceptors([authInterceptor, errorInterceptor]),
  withFetch(),  // uses Fetch API — required for SSR compatibility
)
```

## Typed Service Pattern

```typescript
@Injectable({ providedIn: 'root' })
export class ProspectService {
  private readonly http = inject(HttpClient);
  private readonly env = inject(ENVIRONMENT);

  private get base(): string { return `${this.env.apiBaseUrl}/prospects`; }

  getAll(page = 1, pageSize = 20): Observable<ProspectListResponse> {
    return this.http.get<ProspectListResponse>(this.base, {
      params: { page, pageSize },
    });
  }

  getById(id: string): Observable<Prospect> {
    return this.http.get<Prospect>(`${this.base}/${id}`);
  }

  updateStatus(id: string, status: ProspectStatus): Observable<Prospect> {
    return this.http.patch<Prospect>(`${this.base}/${id}/status`, { status });
  }

  triggerDiscovery(dto: CreateProspectRunDto): Observable<{ jobId: string }> {
    return this.http.post<{ jobId: string }>(`${this.base}/run`, dto);
  }
}
```

## Auth Interceptor

```typescript
// core/http/auth.interceptor.ts
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const auth = inject(AuthService);
  const token = auth.accessToken();
  if (!token) return next(req);
  return next(req.clone({
    setHeaders: { Authorization: `Bearer ${token}` },
  }));
};
```

## Global Error Interceptor

```typescript
// core/http/error.interceptor.ts
export const errorInterceptor: HttpInterceptorFn = (req, next) =>
  next(req).pipe(
    catchError((err: HttpErrorResponse) => {
      const sentry = inject(SentryService);

      // Map to domain error
      const apiError = new ApiError(err.status, extractMessage(err));
      sentry.captureError(apiError, { url: req.url, method: req.method });

      if (err.status === 401) inject(AuthService).logout();
      if (err.status === 403) inject(Router).navigate(['/forbidden']);

      return throwError(() => apiError);
    })
  );

function extractMessage(err: HttpErrorResponse): string {
  return typeof err.error?.message === 'string'
    ? err.error.message
    : 'An unexpected error occurred.';
}
```

## Domain Error Type

```typescript
// core/http/api-error.ts
export class ApiError extends Error {
  constructor(
    public readonly status: number,
    message: string,
  ) {
    super(message);
    this.name = 'ApiError';
  }

  get isNotFound(): boolean { return this.status === 404; }
  get isUnauthorized(): boolean { return this.status === 401; }
  get isServerError(): boolean { return this.status >= 500; }
}
```

## Runtime Validation with Zod (AI responses)

AI-generated payloads may not exactly match the TypeScript interface. Validate at runtime:

```typescript
import { z } from 'zod';

const InsightSchema = z.object({
  hypothesis: z.string(),
  painPoints: z.array(z.string()),
  consultingAngle: z.string(),
});

getInsight(prospectId: string): Observable<Insight> {
  return this.http.get<unknown>(`${this.base}/${prospectId}/insight`).pipe(
    map(raw => InsightSchema.parse(raw)),
    catchError(err => {
      if (err instanceof ZodError) throw new ApiError(422, 'Invalid insight shape from server');
      throw err;
    }),
  );
}
```

## Rules

- No `HttpClient` in components — only in services.
- No `any` for response types — always `http.get<T>()`.
- `HttpErrorResponse` must not leak outside the service — map to `ApiError`.
- All secrets (Exa, Firecrawl API keys) stay on the backend. The frontend calls the NestJS proxy only.
