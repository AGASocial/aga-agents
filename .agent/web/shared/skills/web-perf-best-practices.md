# Performance Best Practices

## Performance Budget

| Metric | Warning | Error (blocks CI) |
|---|---|---|
| Initial JS bundle | 180 KB | 200 KB |
| Any component style | 6 KB | 10 KB |
| Largest Contentful Paint | < 2.5s | — |
| Total Blocking Time | < 200ms | — |

Configured in `angular.json` under `budgets`. CI fails on error threshold.

## 1. OnPush Everywhere
**Impact: CRITICAL**

Reduces change detection to signal/input changes only. Mandatory on every component. See [component-best-practices.md](../../developer-agent/skills/component-best-practices.md).

## 2. Track in @for
**Impact: HIGH**

Without `track`, Angular destroys and recreates every DOM node on any list change.

```html
<!-- Always -->
@for (item of items(); track item.id) { ... }

<!-- If items have no stable ID -->
@for (item of items(); track $index) { ... }  <!-- last resort only -->
```

## 3. Lazy Loading
**Impact: HIGH**

- All feature routes: `loadComponent` / `loadChildren`.
- Heavy third-party libraries (chart.js, rich text editors): `@defer`.
- Icons: import individually, never the full icon pack.

## 4. Deferrable Views
**Impact: HIGH**

```html
<!-- Below the fold -->
@defer (on viewport) {
  <app-signal-timeline [signals]="signals()" />
} @placeholder { <div class="h-48 bg-gray-100 animate-pulse"></div> }

<!-- On user interaction -->
@defer (on interaction) {
  <app-outreach-editor [draft]="draft()" />
} @placeholder { <button>Edit Outreach Draft</button> }

<!-- Prefetch in idle time, render on hover -->
@defer (on hover; prefetch on idle) {
  <app-insight-detail [insight]="insight()" />
}
```

## 5. Computed Signals Over Template Expressions
**Impact: HIGH**

```typescript
// WRONG — recalculates on every CD cycle
// template: {{ prospects().filter(p => p.score === 'HOT').length }}

// CORRECT — recalculates only when prospects() changes
readonly hotCount = computed(() => this.prospects().filter(p => p.score === 'HOT').length);
```

## 6. Image Optimization

```html
<!-- Always use NgOptimizedImage -->
<img
  ngSrc="/assets/company-logo.png"
  width="200"
  height="60"
  priority          <!-- for LCP images above the fold -->
  placeholder       <!-- blurred placeholder while loading -->
/>
```

For external images (e.g., company logos from LinkedIn), configure the image loader:

```typescript
provideImageKitLoader('https://ik.imagekit.io/yourkey')
// or use a custom loader for self-hosted images
```

## 7. Bundle Analysis

```bash
ng build --stats-json
npx webpack-bundle-analyzer dist/web-app/stats.json
```

Run before each major release. Flag any third-party package >20 KB that could be tree-shaken or replaced.

## 8. Virtual Scrolling for Large Lists

When rendering more than ~50 items (e.g., a large prospect list), use CDK Virtual Scroll:

```html
<cdk-virtual-scroll-viewport itemSize="72" class="h-screen">
  @for (prospect of prospects(); track prospect.id) {
    <app-prospect-row *cdkVirtualFor="let prospect of prospects()" [prospect]="prospect" />
  }
</cdk-virtual-scroll-viewport>
```