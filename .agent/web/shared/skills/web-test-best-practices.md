# Testing Best Practices

## Test Stack

| Layer | Tool | Location |
|---|---|---|
| Service unit tests | Jest + Angular TestBed | `*.service.spec.ts` |
| Component tests | `@testing-library/angular` + Jest | `*.component.spec.ts` |
| Store tests | Jest + TestBed injection context | `*.store.spec.ts` |
| E2E / a11y (Phase 2) | Playwright + axe-core | `e2e/` |

## Coverage Requirements

| Layer | Minimum Line Coverage |
|---|---|
| Services | 80% |
| Store services | 80% |
| Components | Key paths — not %  (render, interaction, error state) |

## 1. Service Unit Tests

```typescript
describe('ProspectService', () => {
  let service: ProspectService;
  let httpMock: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [
        ProspectService,
        provideHttpClientTesting(),
        { provide: ENVIRONMENT, useValue: { apiBaseUrl: '' } },
      ],
    });
    service = TestBed.inject(ProspectService);
    httpMock = TestBed.inject(HttpTestingController);
  });

  afterEach(() => httpMock.verify()); // catches unexpected requests

  it('returns typed list on success', () => {
    const mock: ProspectListResponse = { items: [], total: 0, page: 1, pageSize: 20 };
    service.getAll().subscribe(res => expect(res).toEqual(mock));
    httpMock.expectOne('/prospects').flush(mock);
  });

  it('propagates ApiError on 500', () => {
    service.getAll().subscribe({ error: (err: ApiError) => {
      expect(err).toBeInstanceOf(ApiError);
      expect(err.isServerError).toBe(true);
    }});
    httpMock.expectOne('/prospects').flush('error', { status: 500, statusText: 'Server Error' });
  });

  it('returns empty array for 200 with no items', () => {
    const mock: ProspectListResponse = { items: [], total: 0, page: 1, pageSize: 20 };
    service.getAll().subscribe(res => expect(res.items).toHaveLength(0));
    httpMock.expectOne('/prospects').flush(mock);
  });
});
```

## 2. Component Tests

```typescript
describe('ProspectListComponent', () => {
  it('renders a card for each prospect', async () => {
    const prospects = [mockProspect({ id: '1', name: 'Acme' })];
    const { getAllByRole } = await render(ProspectListComponent, {
      providers: [{ provide: ProspectStore, useValue: mockStore({ prospects }) }],
    });
    expect(getAllByRole('article')).toHaveLength(1);
  });

  it('shows spinner while loading', async () => {
    const { getByRole } = await render(ProspectListComponent, {
      providers: [{ provide: ProspectStore, useValue: mockStore({ loading: true }) }],
    });
    expect(getByRole('status')).toBeInTheDocument();
  });

  it('shows error banner on failure', async () => {
    const { getByRole } = await render(ProspectListComponent, {
      providers: [{ provide: ProspectStore, useValue: mockStore({ error: 'Load failed' }) }],
    });
    expect(getByRole('alert')).toHaveTextContent('Load failed');
  });
});
```

## 3. Signal Store Tests

```typescript
describe('ProspectStore', () => {
  let store: ProspectStore;

  beforeEach(() => {
    TestBed.configureTestingModule({ providers: [ProspectStore, provideHttpClientTesting()] });
    store = TestBed.inject(ProspectStore);
  });

  it('hotProspects filters by score', () => {
    TestBed.runInInjectionContext(() => {
      store['_prospects'].set([
        mockProspect({ score: 'HOT' }),
        mockProspect({ score: 'WARM' }),
      ]);
      expect(store.hotProspects()).toHaveLength(1);
    });
  });
});
```

## 4. Rules

| Rule | Rationale |
|---|---|
| `provideHttpClientTesting()` in every test that touches HTTP | Never hit real network in tests |
| `fakeAsync` + `tick()` for timer-dependent code | Deterministic timing |
| Test behavior, not implementation | Assert what the user sees, not which method was called |
| Mock at the service boundary, not the HTTP boundary, for component tests | Tests component + store integration |
| One `describe` per component/service | Easy to locate failures |
| `afterEach(() => httpMock.verify())` | Catches unexpected or missing HTTP calls |

## 5. Test Helpers

Create `src/testing/` with:
- `mock-prospect.ts` — factory function `mockProspect(overrides?)` returning a `Prospect`.
- `mock-store.ts` — factory for creating store doubles with signal overrides.
- `render-helpers.ts` — common `render` wrapper with default providers pre-configured.
