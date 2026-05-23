# Signals & State Management

## Core Primitives

| Primitive | Use for |
|---|---|
| `signal(value)` | Mutable state — source of truth |
| `computed(() => ...)` | Derived values — recalculates lazily |
| `effect(() => ...)` | Side effects reacting to signal changes |
| `toSignal(obs$)` | Bridge an Observable into a read-only signal |
| `toObservable(sig)` | Bridge a signal into an Observable for RxJS operators |
| `resource({ request, loader })` | Async data loading scoped to a component |
| `linkedSignal()` | Reset-on-source-change derived signal |

## Pattern 1 — Component-Local State

For state that lives only within one component:

```typescript
@Component({ standalone: true, changeDetection: ChangeDetectionStrategy.OnPush })
export class ProspectListComponent {
  private readonly store = inject(ProspectStore);

  readonly searchQuery = signal('');
  readonly filtered = computed(() => {
    const q = this.searchQuery().toLowerCase();
    return this.store.prospects().filter(p => p.name.toLowerCase().includes(q));
  });

  setSearch(q: string): void {
    this.searchQuery.set(q);
  }
}
```

## Pattern 2 — Feature Store Service (2+ components share state)

```typescript
@Injectable({ providedIn: 'root' })
export class ProspectStore {
  private readonly service = inject(ProspectService);

  // Private writable
  private readonly _prospects = signal<Prospect[]>([]);
  private readonly _loading = signal(false);
  private readonly _error = signal<string | null>(null);

  // Public read-only
  readonly prospects = this._prospects.asReadonly();
  readonly loading = this._loading.asReadonly();
  readonly error = this._error.asReadonly();

  // Derived
  readonly hotProspects = computed(() => this._prospects().filter(p => p.score === 'HOT'));
  readonly isEmpty = computed(() => !this._loading() && this._prospects().length === 0);

  load(): void {
    this._loading.set(true);
    this._error.set(null);
    this.service.getAll().pipe(
      takeUntilDestroyed(),
    ).subscribe({
      next: list => { this._prospects.set(list); this._loading.set(false); },
      error: err => { this._error.set(err.message); this._loading.set(false); },
    });
  }

  updateStatus(id: string, status: ProspectStatus): void {
    this._prospects.update(list =>
      list.map(p => p.id === id ? { ...p, status } : p)
    );
  }
}
```

## Pattern 3 — Async Resource (component-scoped)

```typescript
readonly prospectId = input.required<string>();

readonly detail = resource({
  request: () => ({ id: this.prospectId() }),
  loader: ({ request, abortSignal }) =>
    firstValueFrom(inject(ProspectService).getById(request.id), { defaultValue: null }),
});

// Template usage
// detail.isLoading() → boolean
// detail.value()    → Prospect | null
// detail.error()    → unknown
```

## When to Escalate to NgRx SignalStore

Introduce `@ngrx/signals` only when:
- State is shared across 4+ unrelated feature modules.
- Complex optimistic updates with rollback are needed.
- The team needs Redux DevTools for debugging.

For everything else — plain injectable signal stores are sufficient and have less boilerplate.

## Rules

| Rule | Rationale |
|---|---|
| Never use `effect()` to sync one signal from another | Use `computed()` — it's lazy and avoids glitches |
| Always initialize signals with a typed default | Prevents `undefined` signals in templates |
| Expose store signals as `asReadonly()` | Prevents external mutation |
| Use `.update()` for immutable array/object changes | Keeps signal identity consistent for `OnPush` |
| Never call `effect()` inside a template | Effects belong in the class constructor |
