# RxJS Best Practices

## Subscription Cleanup — Always

| Strategy | When to Use |
|---|---|
| `async` pipe | Observables bound directly to the template |
| `toSignal(obs$)` | Bridging an Observable into the signal world |
| `takeUntilDestroyed(destroyRef)` | Non-template subscriptions in a component/directive |
| `take(1)` / `first()` | One-shot fire-and-forget that completes immediately |

```typescript
// Preferred — no manual subscription
readonly prospects = toSignal(this.service.getAll(), { initialValue: [] });

// Acceptable — cleanup via destroyRef
constructor() {
  const destroyRef = inject(DestroyRef);
  this.ws.messages$
    .pipe(takeUntilDestroyed(destroyRef))
    .subscribe(msg => this._messages.update(l => [...l, msg]));
}
```

## Operator Selection Guide

| Operator | Use Case | Example |
|---|---|---|
| `switchMap` | Cancel previous, start new | Search typeahead, route data reload |
| `exhaustMap` | Ignore new while current runs | Form submit button |
| `concatMap` | Queue in order | Sequential API writes |
| `mergeMap` | Parallel, independent | Fire-and-forget analytics pings |

```typescript
// Search — switchMap cancels stale requests
readonly results$ = this.searchQuery$.pipe(
  debounceTime(300),
  distinctUntilChanged(),
  filter(q => q.length >= 2),
  switchMap(q => this.service.search(q).pipe(
    catchError(() => of([]))  // never kill the stream
  )),
);

// Form submit — exhaustMap prevents double-submit
onSubmit(): void {
  this.submit$.next(this.form.value);
}
readonly submitResult$ = this.submit$.pipe(
  exhaustMap(dto => this.service.create(dto)),
);
```

## Error Handling in Streams

Never let an unhandled error terminate a long-lived stream (search, WebSocket, polling).

```typescript
this.service.poll().pipe(
  catchError(err => {
    this._error.set(err.message);
    return EMPTY;  // stops this attempt but outer stream can restart
  }),
  retry({ count: 3, delay: 2000 }),
)
```

## Signals Interop

```typescript
// Observable → Signal (read-only)
readonly data = toSignal(this.http.get<Data[]>('/api/data'), {
  initialValue: [] as Data[],
  requireSync: false,
});

// Signal → Observable (for RxJS pipeline)
readonly query$ = toObservable(this.searchQuery);
readonly results$ = this.query$.pipe(
  debounceTime(300),
  switchMap(q => this.service.search(q)),
);
```

## Anti-Patterns

```typescript
// WRONG — nested subscription
this.idStream$.subscribe(id => {
  this.service.getById(id).subscribe(data => ...); // memory leak
});
// CORRECT
this.idStream$.pipe(switchMap(id => this.service.getById(id))).subscribe(...);

// WRONG — subscription without cleanup in a component
ngOnInit() { this.service.updates$.subscribe(...); }
// CORRECT — takeUntilDestroyed or toSignal

// WRONG — BehaviorSubject exposed as a public API
class MyStore { updates$ = new BehaviorSubject([]); }
// CORRECT — expose as signal
class MyStore {
  private readonly _items = signal<Item[]>([]);
  readonly items = this._items.asReadonly();
}
```
