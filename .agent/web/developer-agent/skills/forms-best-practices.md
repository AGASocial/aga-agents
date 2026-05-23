# Forms Best Practices

Always use **Typed Reactive Forms**. Template-driven forms are not used in this project.

## 1. Typed FormGroup
**Impact: CRITICAL**

```typescript
interface IntakeForm {
  websiteUrl: FormControl<string>;
  linkedInUrl: FormControl<string | null>;
  targetBuyer: FormControl<string>;
}

readonly form = new FormGroup<IntakeForm>({
  websiteUrl: new FormControl('', {
    nonNullable: true,
    validators: [Validators.required, Validators.pattern(/^https?:\/\/.+/)],
  }),
  linkedInUrl: new FormControl<string | null>(null, [
    Validators.pattern(/linkedin\.com\/company\/.+/),
  ]),
  targetBuyer: new FormControl('', { nonNullable: true, validators: [Validators.required] }),
});
```

## 2. Submit Pattern

```typescript
readonly isLoading = signal(false);
readonly serverError = signal<string | null>(null);

onSubmit(): void {
  if (this.form.invalid) {
    this.form.markAllAsTouched();
    return;
  }

  this.isLoading.set(true);
  this.serverError.set(null);

  this.service.submit(this.form.getRawValue()).pipe(
    takeUntilDestroyed(this.destroyRef),
    finalize(() => this.isLoading.set(false)),
  ).subscribe({
    next: result => this.router.navigate(['/prospects', result.id]),
    error: (err: ApiError) => {
      if (err.status === 422 && err.fieldErrors) {
        // Bind server errors back to controls
        Object.entries(err.fieldErrors).forEach(([field, msg]) => {
          this.form.get(field)?.setErrors({ server: msg });
        });
      } else {
        this.serverError.set(err.message);
      }
    },
  });
}
```

## 3. Template Conventions

```html
<form [formGroup]="form" (ngSubmit)="onSubmit()" novalidate>

  <!-- Field with accessibility -->
  <div>
    <label for="websiteUrl">Company Website *</label>
    <input
      id="websiteUrl"
      type="url"
      formControlName="websiteUrl"
      [attr.aria-invalid]="form.controls.websiteUrl.invalid && form.controls.websiteUrl.touched"
      aria-describedby="websiteUrl-error"
      autocomplete="url"
    />
    @if (form.controls.websiteUrl.errors?.['required'] && form.controls.websiteUrl.touched) {
      <span id="websiteUrl-error" role="alert">Website URL is required.</span>
    }
    @if (form.controls.websiteUrl.errors?.['pattern'] && form.controls.websiteUrl.touched) {
      <span id="websiteUrl-error" role="alert">Enter a valid URL starting with https://</span>
    }
    @if (form.controls.websiteUrl.errors?.['server']) {
      <span id="websiteUrl-error" role="alert">{{ form.controls.websiteUrl.errors?.['server'] }}</span>
    }
  </div>

  <!-- Server-level error -->
  @if (serverError()) {
    <div role="alert" class="error-banner">{{ serverError() }}</div>
  }

  <!-- Submit -->
  <button type="submit" [disabled]="isLoading()">
    @if (isLoading()) { Submitting... } @else { Submit }
  </button>

</form>
```

## 4. Custom Validators

```typescript
// Sync validator
export function httpsUrlValidator(): ValidatorFn {
  return (ctrl: AbstractControl): ValidationErrors | null => {
    if (!ctrl.value) return null;
    return ctrl.value.startsWith('https://') ? null : { httpsRequired: true };
  };
}

// Async validator (debounced)
export function uniqueEmailValidator(service: UserService): AsyncValidatorFn {
  return (ctrl: AbstractControl): Observable<ValidationErrors | null> =>
    ctrl.valueChanges.pipe(
      debounceTime(400),
      distinctUntilChanged(),
      switchMap(email => service.checkEmailAvailable(email)),
      map(available => available ? null : { emailTaken: true }),
      take(1),
    );
}
```

## 5. Rules

- `nonNullable: true` on controls that should never be null.
- `form.getRawValue()` instead of `form.value` — includes disabled controls.
- Never disable the submit button based on `form.invalid` alone (blocks assistive tech); instead mark all touched and show errors.
- Every form field must have a visible `<label>` with a matching `for` / `id` pair.
- Placeholders are not labels — they disappear when the user types.
