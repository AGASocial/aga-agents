# Routing Best Practices

## 1. Lazy Load Every Feature
**Impact: CRITICAL**

```typescript
// app.routes.ts
export const routes: Routes = [
  { path: '', redirectTo: 'dashboard', pathMatch: 'full' },
  {
    path: 'intake',
    canActivate: [authGuard],
    loadChildren: () => import('./features/client-intake/client-intake.routes')
      .then(m => m.CLIENT_INTAKE_ROUTES),
  },
  {
    path: 'prospects',
    canActivate: [authGuard],
    loadChildren: () => import('./features/prospect-discovery/prospect-discovery.routes')
      .then(m => m.PROSPECT_ROUTES),
  },
  {
    path: 'dashboard',
    canActivate: [authGuard],
    loadComponent: () => import('./features/dashboard/components/dashboard-shell/dashboard-shell.component')
      .then(m => m.DashboardShellComponent),
  },
  {
    path: 'login',
    loadComponent: () => import('./core/auth/login/login.component').then(m => m.LoginComponent),
  },
  {
    path: '**',
    loadComponent: () => import('./core/not-found/not-found.component').then(m => m.NotFoundComponent),
  },
];
```

## 2. Functional Guards (Angular 15+)

Never use class-based guards for new code.

```typescript
// core/auth/auth.guard.ts
export const authGuard: CanActivateFn = (_route, state) => {
  const auth = inject(AuthService);
  const router = inject(Router);
  if (auth.isAuthenticated()) return true;
  return router.createUrlTree(['/login'], { queryParams: { returnUrl: state.url } });
};

// Role guard factory
export const roleGuard = (role: UserRole): CanActivateFn => () => {
  const auth = inject(AuthService);
  const router = inject(Router);
  return auth.hasRole(role) ? true : router.createUrlTree(['/forbidden']);
};
```

## 3. Resolvers for Required Data

Use resolvers when the route requires data to render — avoids a flash of empty state.

```typescript
// features/prospect-discovery/prospect.resolver.ts
export const prospectResolver: ResolveFn<Prospect> = route =>
  inject(ProspectService).getById(route.paramMap.get('id')!);

// Route
{
  path: ':id',
  resolve: { prospect: prospectResolver },
  loadComponent: () => import('./components/prospect-detail/prospect-detail.component')
    .then(m => m.ProspectDetailComponent),
}

// Component — received automatically via withComponentInputBinding()
readonly prospect = input.required<Prospect>();
```

## 4. Component Input Binding

Enable `withComponentInputBinding()` in `provideRouter`. Route params, query params, and resolved data are bound directly as component `input()` signals — no need to inject `ActivatedRoute`.

```typescript
// app.config.ts
provideRouter(routes, withComponentInputBinding())

// Component
readonly id = input.required<string>();           // :id param
readonly tab = input<string>('overview');         // ?tab= query param
readonly prospect = input.required<Prospect>();   // resolve: { prospect }
```

## 5. Route-Scoped Providers

Scope services to a route subtree to reset state when the user navigates away:

```typescript
{
  path: 'prospects',
  providers: [ProspectStore],   // new instance per route activation
  loadChildren: () => ...,
}
```

## 6. Preloading Strategy

For authenticated apps, preload feature routes after initial load:

```typescript
provideRouter(routes,
  withComponentInputBinding(),
  withPreloading(PreloadAllModules),  // or a custom selective strategy
)
```

## 7. Named Router Outlets (Use Sparingly)

Avoid named outlets unless the UX genuinely requires parallel views (e.g., a side-panel that persists across routes). They add complexity without proportional benefit.
