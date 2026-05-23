# Android Code Review Checklist

This checklist ensures that Android code meets high standards for performance, security, and maintainability.

## 1. General Kotlin
- [ ] Uses modern Kotlin features (e.g., `when`, `let`, `also`, `run`).
- [ ] Avoids `null` where possible; uses safe calls or `!!` only when absolutely necessary.
- [ ] Proper use of `val` vs `var`.

## 2. Jetpack Compose
- [ ] State is hoisted appropriately.
- [ ] Composables are stateless (parameters passed in).
- [ ] Uses `remember` and `rememberSaveable` correctly.
- [ ] Avoids side effects in the body of Composables.
- [ ] Includes `@Preview` for multiple configurations.

## 3. Architecture & Data
- [ ] Follows MVVM/Clean Architecture.
- [ ] Repository is the single source of truth.
- [ ] Network calls use `suspend` and proper error handling.
- [ ] Local database operations are performed off the main thread.

## 4. Performance
- [ ] Avoids memory leaks (no long-lived references to Context or Views).
- [ ] Minimizes recompositions in Compose.
- [ ] Uses optimized image loading (e.g., Coil).

## 5. Security
- [ ] Does not store sensitive data (API keys, tokens) in plain text.
- [ ] Uses `EncryptedSharedPreferences` or DataStore for sensitive info.
- [ ] Validates user input.

## 6. Testing
- [ ] Business logic has unit tests.
- [ ] Complex UI components have Compose tests.
- [ ] Edge cases are covered.
