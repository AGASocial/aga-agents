# Architecture Best Practices

## 1. Feature-Based Folder Structure
**Impact: CRITICAL**

Organize by feature domain, not by type. Each feature is a self-contained vertical slice.

```
src/app/
├── core/                          # Singleton services, interceptors, guards, app-wide tokens
│   ├── auth/                      # AuthService, authGuard, token management
│   ├── http/                      # authInterceptor, errorInterceptor
│   ├── layout/                    # AppShellComponent, NavComponent, SidebarComponent
│   ├── observability/             # SentryService, PerformanceService
│   └── tokens/                    # ENVIRONMENT and other InjectionTokens
├── shared/                        # Reusable, domain-agnostic standalone components/pipes/directives
│   ├── components/
│   │   ├── spinner/
│   │   ├── error-banner/
│   │   ├── empty-state/
│   │   └── badge/
│   ├── pipes/
│   │   └── truncate.pipe.ts
│   └── directives/
│       └── auto-focus.directive.ts
├── features/
│   ├── client-intake/             # US-01
│   ├── icp-generation/            # US-02
│   ├── prospect-discovery/        # US-03
│   ├── signal-detection/          # US-04
│   ├── opportunity-scoring/       # US-05
│   ├── insight-generation/        # US-06
│   └── dashboard/                 # US-09 — Prospect Workspace
├── app.component.ts
├── app.routes.ts                  # Root routes — all features lazy-loaded
└── app.config.ts                  # provideRouter, provideHttpClient, providers
```

## 2. Standalone Components Only
**Impact: CRITICAL**

No `NgModule` for new code. Every component, directive, and pipe is `standalone: true`. Import only what each component directly uses — no kitchen-sink imports.

```typescript
@Component({
  standalone: true,
  imports: [RouterLink, AsyncPipe, ProspectCardComponent, SpinnerComponent],
  changeDetection: ChangeDetectionStrategy.OnPush,
})
```

## 3. Dependency Direction (One-Way)
**Impact: CRITICAL**

```
features/ → shared/ → core/   (allowed)
features/ → features/          (FORBIDDEN — cross-feature imports)
core/     → features/          (FORBIDDEN)
shared/   → core/              (FORBIDDEN — shared must be domain-agnostic)
```

Cross-feature communication routes through:
- The Angular Router (navigation)
- A `core/` shared store service
- Route query parameters

## 4. Barrel Exports
**Impact: HIGH**

Each feature exposes only its public API through `index.ts`. Never import from deep paths outside the feature.

```typescript
// features/prospect-discovery/index.ts
export { ProspectDiscoveryRoutes } from './prospect-discovery.routes';
// Internal components are NOT re-exported — they are private
```

## 5. Single Responsibility
**Impact: HIGH**

- One component per file.
- Services handle one domain concern each (`ProspectService` fetches prospects; `ScoringService` scores them).
- No "utils.ts" catch-all files — utilities belong to the domain that uses them.

## 6. Core Services as Singletons

All `core/` services use `providedIn: 'root'`. Feature services can be `providedIn: 'root'` or scoped to a route — decide based on whether the state should survive navigation.

```typescript
// Route-scoped store (state resets when leaving the route)
{
  path: 'prospects',
  providers: [ProspectStore],
  loadComponent: ...,
}
```
