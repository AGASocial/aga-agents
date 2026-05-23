# Component Best Practices

## 1. OnPush — No Exceptions
**Impact: CRITICAL**

```typescript
@Component({
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  // ...
})
```

Angular will only check this component when:
- An `input()` signal reference changes
- An event fires from inside the component
- An `async` pipe resolves
- `markForCheck()` is called (avoid — use signals instead)

## 2. Smart vs. Dumb Split
**Impact: HIGH**

| Smart (Shell) | Dumb (Presentational) |
|---|---|
| Injects store/service | Receives data via `input()` |
| Owns signals and async calls | Emits user actions via `output()` |
| One per feature route | Many per feature |
| `<feature>-shell.component.ts` | `<widget>.component.ts` |

```typescript
// Dumb — pure input/output contract
@Component({
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  selector: 'app-prospect-card',
})
export class ProspectCardComponent {
  readonly prospect = input.required<Prospect>();
  readonly statusChange = output<{ id: string; status: ProspectStatus }>();

  onStatusSelect(status: ProspectStatus): void {
    this.statusChange.emit({ id: this.prospect().id, status });
  }
}
```

## 3. Signal-Based Inputs & Outputs (Angular 17.1+)

```typescript
// Preferred for new components
readonly title = input.required<string>();
readonly count = input(0);               // default value
readonly items = input<Item[]>([]);
readonly selected = output<Item>();

// Transform on input
readonly id = input.required<string, number>({ transform: numberAttribute });
```

## 4. Modern Control Flow

Always use built-in control flow. Structural directives (`*ngIf`, `*ngFor`) are deprecated for new code.

```html
<!-- Conditional -->
@if (loading()) {
  <app-spinner />
} @else if (error()) {
  <app-error-banner [message]="error()!" />
} @else {
  <!-- main content -->
}

<!-- List — always include track -->
@for (prospect of filtered(); track prospect.id) {
  <app-prospect-card
    [prospect]="prospect"
    (statusChange)="onStatusChange($event)"
  />
} @empty {
  <app-empty-state message="No prospects found." />
}

<!-- Deferred loading -->
@defer (on viewport) {
  <app-insights-chart [data]="insights()" />
} @loading (minimum 200ms) {
  <app-skeleton-chart />
} @error {
  <p role="alert">Failed to load chart.</p>
}
```

## 5. Lifecycle — Prefer Signals Over Hooks

```typescript
// Old pattern — avoid for new code
ngOnInit(): void {
  this.service.getProspects().subscribe(p => this.prospects = p);
}

// Preferred — signals with toSignal
readonly prospects = toSignal(inject(ProspectService).getAll(), { initialValue: [] });
```

If manual cleanup is needed, use `DestroyRef`:

```typescript
constructor() {
  const destroyRef = inject(DestroyRef);
  inject(WebSocketService).messages$
    .pipe(takeUntilDestroyed(destroyRef))
    .subscribe(msg => this._messages.update(list => [...list, msg]));
}
```

## 6. Host Bindings

Use `host` in the decorator instead of `@HostBinding` / `@HostListener`:

```typescript
@Component({
  host: {
    class: 'block w-full',
    '[attr.aria-busy]': 'loading()',
    '(keydown.escape)': 'onEscape()',
  },
})
```

## 7. Template Best Practices

- Extract repeated logic into `computed()` signals — never repeat filter/map in the template.
- Use `ng-container` to apply `@if` / `@for` without adding DOM nodes.
- Avoid method calls in templates unless they are signal accessors (`()`). Method calls re-execute on every CD cycle under non-OnPush parents.
