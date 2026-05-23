# Web Code Review

You are performing a thorough Angular code review. Protect production quality, security, and team standards — do not rubber-stamp work.

## Review Philosophy

- Every comment must reference a specific skill file and rule. No vague "this could be better."
- Severity levels: **BLOCKER** (must fix before merge), **MAJOR** (strong expectation to fix), **MINOR** (suggestion).
- Assume nothing about intent — if the code is ambiguous, call it out.

Label each checklist finding: **BLOCKER** | **MAJOR** | **MINOR**. All BLOCKERs must be resolved before merge.

For PRs that touch authentication, authorization, forms, API integration, AI-generated content rendering, or user-generated data, also run [security-review.md](./security-review.md).

## Review Sequence

Run these passes in order before using the checklist below:

### Pass 1 — Architecture
Refer to [web-arch-best-practices.md](../../shared/skills/web-arch-best-practices.md).
- Is the feature in the right folder?
- Are feature boundaries respected (no cross-feature imports)?
- Is `core/` accessed only from features and shared, not the other way?

### Pass 2 — Component Quality
Refer to [component-best-practices.md](../../developer-agent/skills/component-best-practices.md).
- `standalone: true` on every component?
- `ChangeDetectionStrategy.OnPush` on every component?
- Smart/dumb split maintained?
- New Angular control flow (`@if`, `@for`, `@defer`) used — not `*ngIf`, `*ngFor`?

### Pass 3 — State & Signals
Refer to [signals-state.md](../../developer-agent/skills/signals-state.md).
- State is `signal()` — no raw class properties used as state?
- Derived values use `computed()` — not manually synced signals?
- No `effect()` used where `computed()` would suffice?

### Pass 4 — RxJS Hygiene
Refer to [rxjs-best-practices.md](../../developer-agent/skills/rxjs-best-practices.md).
- Every `.subscribe()` has a cleanup strategy?
- No nested subscriptions?
- Correct flattening operator (`switchMap` / `concatMap` / `exhaustMap`) for the use case?

### Pass 5 — HTTP & Types
Refer to [http-api.md](../../developer-agent/skills/http-api.md).
- No `any` in HTTP response types?
- Errors mapped to domain types — `HttpErrorResponse` not leaking to components?
- Auth and error interceptors applied globally?

### Pass 6 — Forms
Refer to [forms-best-practices.md](../../developer-agent/skills/forms-best-practices.md).
- Typed `FormGroup<{...}>` — no untyped `FormGroup`?
- Server-side errors bound to form controls?
- Submit button disabled while loading?

### Pass 7 — Security
Refer to [web-security-best-practices.md](../../shared/skills/web-security-best-practices.md) and run [security-review.md](./security-review.md) for sensitive PRs.
- No `innerHTML` without `DomSanitizer`?
- No secrets in the bundle?
- All protected routes have `canActivate: [authGuard]`?

### Pass 8 — Performance
Refer to [web-perf-best-practices.md](../../shared/skills/web-perf-best-practices.md).
- All `@for` have `track`?
- No expensive computation in the template — use `computed()`?
- New lazy route added for the feature?
- `@defer` used for below-the-fold or heavy content?

### Pass 9 — Accessibility
Refer to [web-ui-best-practices.md](../../shared/skills/web-ui-best-practices.md).
- All interactive elements keyboard-reachable?
- All form fields have labels (not just placeholders)?
- Dynamic content changes announced to screen readers?

### Pass 10 — Tests
Refer to [web-test-best-practices.md](../../shared/skills/web-test-best-practices.md).
- Service has success + error path tests?
- Component has a render test and key interaction test?
- No real HTTP in tests (`provideHttpClientTesting`)?
- Coverage ≥80% on the service?

---

## Checklist

## 1. TypeScript & General Code Quality

- [ ] `strict: true` in tsconfig — no overrides.
- [ ] Zero `any`. Every variable, parameter, and return type is explicit.
- [ ] No unguarded type assertions (`as SomeType`) without an inline justification comment.
- [ ] No commented-out code blocks.
- [ ] No `console.log` / `console.error` left in — use the observability service.
- [ ] Dead code removed (unused imports, variables, methods).
- [ ] Enums defined as `const enum` or union types — no numeric enums.

## 2. Architecture & Folder Structure

- [ ] Feature code lives in `src/app/features/<feature-name>/`.
- [ ] No cross-feature imports (feature A does not import from feature B).
- [ ] `core/` services are not imported directly by components — accessed via DI.
- [ ] Shared components/pipes/directives live in `src/app/shared/` and have no feature-specific logic.
- [ ] Barrel `index.ts` exposes only the public API of the feature.

## 3. Component Design

- [ ] `standalone: true` on every component, directive, and pipe.
- [ ] `ChangeDetectionStrategy.OnPush` declared on every component.
- [ ] Smart/dumb split: shell component injects services; presentational components only use `input()` / `output()`.
- [ ] No HTTP calls or business logic inside lifecycle hooks or templates.
- [ ] Signal-based `input()` / `output()` used for new components (Angular 17.1+).
- [ ] `DestroyRef` used for cleanup instead of `implements OnDestroy`.

## 4. Angular Control Flow

- [ ] New control flow used: `@if`, `@for`, `@switch`, `@defer` — not structural directives.
- [ ] Every `@for` has a `track` expression referencing a stable unique ID.
- [ ] `@empty` block provided in `@for` for lists that may be empty.
- [ ] `@defer` used for heavy/non-critical content (charts, detail panels).

## 5. Signals & State

- [ ] All mutable state is `signal()` — not plain class properties.
- [ ] All derived values are `computed()` — no manual sync between signals.
- [ ] `effect()` used only for genuine side effects (DOM interaction, logging, external sync).
- [ ] Signal stores are `Injectable({ providedIn: 'root' })` or scoped to a route as appropriate.
- [ ] `asReadonly()` used to expose signals publicly from stores.

## 6. RxJS & Subscriptions

- [ ] No bare `.subscribe()` without a cleanup strategy.
- [ ] `async` pipe, `toSignal()`, or `takeUntilDestroyed()` used for all subscriptions.
- [ ] No nested subscriptions — flattening operator used (`switchMap`, `concatMap`, `exhaustMap`, `mergeMap`).
- [ ] Correct operator for the use case: `switchMap` for cancellable (search), `exhaustMap` for submit, `concatMap` for ordered.
- [ ] `catchError` returns `EMPTY` or a fallback — never lets the stream die silently.

## 7. HTTP & API Layer

- [ ] All HTTP calls are in services — never in components.
- [ ] `HttpClient` methods typed with `<T>` — no raw `any` responses.
- [ ] `HttpErrorResponse` mapped to a domain `ApiError` type before leaving the service.
- [ ] Auth interceptor applies the Bearer token.
- [ ] Global error interceptor handles 401 (logout) and 403 (forbidden redirect).
- [ ] No API keys or base URLs hardcoded — use `environment.apiBaseUrl` or the `ENVIRONMENT` token.

## 8. Reactive Forms

- [ ] Typed `FormGroup<{ field: FormControl<string> }>` — not `FormGroup` (untyped).
- [ ] `Validators` applied to all required fields.
- [ ] Custom async validators use `AbstractControl` and return `ValidationErrors | null`.
- [ ] Submit handler checks `form.valid` before dispatching.
- [ ] Loading state disables the submit button (`[disabled]="isLoading()"`).
- [ ] Server errors are bound back to form controls (`form.get('field')?.setErrors(...)`).
- [ ] All form fields have associated `<label>` elements — not just placeholders.

## 9. Routing

- [ ] Feature routes use `loadComponent` or `loadChildren` — no eager loading.
- [ ] Protected routes declare `canActivate: [authGuard]`.
- [ ] Resolvers load data required before the route activates.
- [ ] `withComponentInputBinding()` enabled in `provideRouter` — route params as component inputs.
- [ ] A `**` catch-all route maps to the NotFound component.

## 10. Security

- [ ] No `[innerHTML]` binding without `DomSanitizer.bypassSecurityTrustHtml` — and even then, only on server-controlled content.
- [ ] No secrets (API keys, tokens) in the Angular bundle or `environment.ts`.
- [ ] Auth tokens stored in memory (signal) — not `localStorage`.
- [ ] All form inputs validated client-side (UX) and rely on server-side validation for security.
- [ ] `HttpClientXsrfModule` or equivalent CSRF protection configured.

## 11. Performance

- [ ] Initial JS chunk has not grown by >5 KB compressed without approval.
- [ ] `NgOptimizedImage` used for all `<img>` elements.
- [ ] No expensive filtering/mapping in the template — moved to `computed()`.
- [ ] Lazy-loaded feature route registered.
- [ ] `@defer` with `on viewport` or `on interaction` for below-the-fold content.

## 12. Accessibility

- [ ] All interactive elements reachable via keyboard (`Tab`, `Enter`, `Space`, `Escape`).
- [ ] Focus managed correctly on route transitions and modal open/close.
- [ ] All images have meaningful `alt` text (or `alt=""` if decorative).
- [ ] Error messages associated with form fields via `aria-describedby`.
- [ ] Color is not the only means of conveying information.
- [ ] Tested with a screen reader (VoiceOver / NVDA) for key flows.

## 13. Error Handling

- [ ] Every async operation exposes `loading`, `data`, and `error` signals.
- [ ] Template renders a meaningful error state — never blank.
- [ ] Errors are reported to Sentry with context (user ID, feature, operation).

## 14. Tests

- [ ] Service has unit tests: success path, empty response, HTTP error.
- [ ] Component has: render test, key interaction test, error state test.
- [ ] No real HTTP in tests — `provideHttpClientTesting()` used.
- [ ] No real timers — `fakeAsync` / `jest.useFakeTimers()`.
- [ ] Service line coverage ≥80%.
- [ ] Edge cases covered: empty list, null fields, pagination boundaries.
