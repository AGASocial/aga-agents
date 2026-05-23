# Feature Implementation Workflow

This is the mandatory checklist for every new feature. Do not skip steps.

## Pre-Implementation

- [ ] Read the user story in `business/business-analyst/user-stories/`.
- [ ] Identify the API endpoints needed. Check if the NestJS backend already exposes them.
- [ ] If new endpoints are needed, follow the [API Contract Workflow](./api-contract-workflow.md) first.
- [ ] Read [arch-best-practices.md](./arch-best-practices.md) — confirm where this feature lives in the folder structure.

## Step 1 — Scaffold the Feature

```
src/app/features/<feature-name>/
├── <feature-name>.routes.ts
├── components/
│   ├── <feature>-shell/          # Smart container — one per route
│   │   ├── <feature>-shell.component.ts
│   │   └── <feature>-shell.component.html
│   └── <widget>/                 # Dumb presentational — as many as needed
│       ├── <widget>.component.ts
│       └── <widget>.component.html
├── models/
│   └── <feature>.model.ts        # TypeScript interfaces — source of truth
├── services/
│   └── <feature>.service.ts      # HTTP calls + data transformation
└── store/                        # Only if state is shared across components
    └── <feature>.store.ts
```

Generate with Angular CLI:
```bash
ng g c features/<feature-name>/components/<feature>-shell --standalone --change-detection OnPush
ng g s features/<feature-name>/services/<feature>
```

## Step 2 — Define the Model

In `<feature>/models/<feature>.model.ts`, define all TypeScript interfaces before writing service or component code. These must match the backend contract (see [api-contract-workflow.md](./api-contract-workflow.md)).

## Step 3 — Implement the Service

- Reference [http-api.md](./http-api.md).
- Return typed `Observable<T>` or use Angular `resource()` for component-scoped fetches.
- Map HTTP errors to domain error types — never expose `HttpErrorResponse` outside the service.

## Step 4 — Implement State (if needed)

- Reference [signals-state.md](./signals-state.md).
- For component-local state: `signal()` / `computed()` in the shell component.
- For state shared across 2+ components: create a `<feature>.store.ts` injectable store service.
- For state shared across 3+ features: escalate to a `core/` store or discuss NgRx adoption.

## Step 5 — Build the UI

- Reference [component-best-practices.md](./component-best-practices.md).
- Shell component injects the store/service and passes data down via `input()`.
- Presentational components are stateless — `input()` in, `output()` out.
- Use `@if / @for / @defer` control flow. No `*ngIf`, `*ngFor`.
- All `@for` loops have a `track` expression.
- Every interactive element is keyboard-accessible and labelled (see [accessibility-best-practices.md](./accessibility-best-practices.md)).

## Step 6 — Wire the Route

In `<feature-name>.routes.ts`:
```typescript
export const FEATURE_ROUTES: Routes = [
  {
    path: '',
    canActivate: [authGuard],
    loadComponent: () => import('./components/<feature>-shell/<feature>-shell.component')
      .then(m => m.FeatureShellComponent),
  },
];
```
Register in `app.routes.ts` with `loadChildren`.

## Step 7 — Handle Errors

- Reference [error-handling.md](./error-handling.md).
- Every async operation must expose a loading signal, a data signal, and an error signal.
- The template must render a meaningful error state — no silent failures.

## Step 8 — Write Tests

- Reference [test-best-practices.md](./test-best-practices.md).
- Service: test success path, empty response, and error path.
- Component: render test, key user interaction, error state render.
- Minimum coverage: 80% line coverage on the service.

## Step 9 — Self-Review

Run the full [web-code-review.md](./web-code-review.md) checklist.
For auth or data-sensitive features, also run [security-review.md](./security-review.md).
