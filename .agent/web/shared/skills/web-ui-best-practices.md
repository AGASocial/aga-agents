# Accessibility Best Practices

Target: **WCAG 2.1 AA** on all user-facing surfaces.

## 1. Keyboard Navigation
**Impact: CRITICAL**

Every interactive element must be reachable and operable via keyboard alone.

- All `<button>`, `<a>`, `<input>`, `<select>` elements are keyboard-focusable by default — do not override with `tabindex="-1"` unless managing focus programmatically.
- Custom interactive elements (clickable `<div>`) must have `role="button"` and `tabindex="0"` and handle `keydown.enter` + `keydown.space`.
- Keyboard trap in modals: focus must cycle within the modal while it's open; `Escape` closes it.
- Visible focus indicator must be present — do not use `outline: none` without a custom replacement.

```typescript
@Component({
  host: {
    'role': 'button',
    'tabindex': '0',
    '(keydown.enter)': 'activate()',
    '(keydown.space)': '$event.preventDefault(); activate()',
  },
})
export class CustomButtonComponent { ... }
```

## 2. Semantic HTML
**Impact: CRITICAL**

Use the correct element for the job. ARIA is a supplement, not a replacement.

```html
<!-- Correct -->
<button (click)="submit()">Submit</button>
<nav aria-label="Main navigation">...</nav>
<main>...</main>
<h1>Dashboard</h1>

<!-- Wrong — div soup with ARIA bolted on -->
<div role="button" (click)="submit()">Submit</div>
```

## 3. Form Accessibility
**Impact: CRITICAL**

- Every form field has a visible `<label>` with a `for` attribute matching the input's `id`.
- Placeholders are not labels.
- Error messages linked via `aria-describedby`.
- Required fields indicated via `aria-required="true"` or `required`.
- Invalid fields indicated via `aria-invalid="true"`.

```html
<label for="company">Company Name *</label>
<input
  id="company"
  formControlName="company"
  [attr.aria-required]="true"
  [attr.aria-invalid]="form.controls.company.invalid && form.controls.company.touched"
  aria-describedby="company-error"
/>
<span id="company-error" role="alert" *ngIf="showError('company')">
  Company name is required.
</span>
```

## 4. Images & Icons

```html
<!-- Informative image -->
<img ngSrc="/assets/logo.svg" alt="Application logo" width="120" height="40" />

<!-- Decorative image -->
<img ngSrc="/assets/bg.svg" alt="" width="800" height="400" aria-hidden="true" />

<!-- Icon-only button -->
<button aria-label="Close dialog" (click)="close()">
  <mat-icon aria-hidden="true">close</mat-icon>
</button>
```

## 5. Dynamic Content & Live Regions

When content updates without a page reload, announce it to screen readers:

```html
<!-- Polite — reads after current content -->
<div aria-live="polite" aria-atomic="true">
  @if (successMessage()) { <span>{{ successMessage() }}</span> }
</div>

<!-- Assertive — interrupts immediately (use sparingly, e.g. errors) -->
<div role="alert">
  @if (error()) { {{ error() }} }
</div>
```

## 6. Color & Contrast

- Text contrast ratio: ≥4.5:1 for normal text, ≥3:1 for large text (WCAG AA).
- Do not use color alone to convey state (e.g., prospect score badge must have text label + color, not color alone).
- Focus indicator contrast: ≥3:1 against adjacent colors.

## 7. Route Change Announcements

Angular does not announce route changes to screen readers by default. Implement a `RouteAnnouncerService`:

```typescript
@Injectable({ providedIn: 'root' })
export class RouteAnnouncerService {
  constructor() {
    inject(Router).events.pipe(
      filter(e => e instanceof NavigationEnd),
      takeUntilDestroyed(),
    ).subscribe(() => {
      // Update a visually-hidden live region with the new page title
      const title = inject(Title).getTitle();
      this.announce(`Navigated to ${title}`);
    });
  }
}
```

Register in `app.config.ts` via `APP_INITIALIZER` or inject in `AppComponent`.

## 8. Testing

- Use `axe-core` via `@axe-core/angular` in component tests to catch WCAG violations automatically.
- Manual test key flows with VoiceOver (macOS) or NVDA (Windows) before each release.
- Keyboard-only walkthrough: tab through the full prospect discovery flow without touching the mouse.
