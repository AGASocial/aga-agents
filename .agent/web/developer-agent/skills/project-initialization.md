# Project Initialization (Tooling Reference)

> **Start here for infra:** [fe-project-setup.md](./fe-project-setup.md) (scaffold + local run), [fe-ci-cd.md](./fe-ci-cd.md), [fe-deployment-and-environments.md](./fe-deployment-and-environments.md).

This skill lists optional packages and config snippets when extending an existing `apps/web` project.

## Required Packages (add incrementally)

```bash
cd apps/web

# Linting & Formatting
ng add @angular-eslint/schematics
npm i -D prettier eslint-config-prettier

# Testing (when migrating from Karma to Jest)
npm i -D jest jest-preset-angular @testing-library/angular @testing-library/user-event

# HTTP & Validation
npm i zod

# Observability
npm i @sentry/angular

# Accessibility (E2E phase)
npm i -D @axe-core/playwright

# Optional: Signal Store (when cross-feature state grows)
npm i @ngrx/signals
```

## tsconfig.json — Mandatory Flags

Ensure these are set (Angular 21 strict scaffold includes most):

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitOverride": true,
    "noPropertyAccessFromIndexSignature": true,
    "noImplicitReturns": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

## angular.json — Build Budget

```json
"budgets": [
  { "type": "initial", "maximumWarning": "180kb", "maximumError": "200kb" },
  { "type": "anyComponentStyle", "maximumWarning": "6kb", "maximumError": "10kb" }
]
```

## App Bootstrap (Standalone)

See `apps/web/src/app/app.config.ts` for the live config. Target shape:

```typescript
export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes, withComponentInputBinding()),
    provideHttpClient(withInterceptors([authInterceptor, errorInterceptor]), withFetch()),
    provideAnimationsAsync(),
    { provide: ENVIRONMENT, useValue: environment },
    { provide: ErrorHandler, useClass: SentryErrorHandler },
  ],
};
```

## ESLint Config (when added)

```json
"@angular-eslint/prefer-standalone": "error",
"@angular-eslint/use-component-change-detection": ["error", { "enumValue": "OnPush" }],
"@typescript-eslint/no-explicit-any": "error",
"no-console": ["error", { "allow": ["warn", "error"] }]
```

## CI

See [fe-ci-cd.md](./fe-ci-cd.md) for the full GitHub Actions workflow.
