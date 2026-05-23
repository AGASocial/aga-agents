# Jetpack Compose Best Practices

This skill outlines best practices for building modern, performant, and maintainable UIs with Jetpack Compose.

## 1. State Management
- **Hoisting State**: Move state up to the caller to make components stateless and reusable.
- **Use `rememberSaveable`**: For state that should survive configuration changes.
- **Prefer `collectAsStateWithLifecycle`**: For collecting flows in a lifecycle-aware manner.

```kotlin
@Composable
fun SearchScreen(viewModel: SearchViewModel = viewModel()) {
    val uiState by viewModel.uiState.collectAsStateWithLifecycle()
    SearchContent(
        query = uiState.query,
        onQueryChange = viewModel::onQueryChange
    )
}
```

## 2. Recomposition Optimization
- **Stable Parameters**: Ensure parameters passed to Composables are stable or immutable.
- **Use `derivedStateOf`**: For state that depends on other state to avoid unnecessary recompositions.
- **`remember` keys**: Use specific keys for `remember` to recalculate only when necessary.

## 3. UI Components
- **Slot API**: Use "slots" (lambda parameters) to make components flexible.
- **Theming**: Use `MaterialTheme` for colors, typography, and shapes.
- **Preview**: Always provide `@Preview` for different UI states (Light/Dark mode, different screen sizes).

## 4. Accessibility
- **Content Description**: Always provide `contentDescription` for images and icons.
- **Click Labels**: Use `Modifier.clickable(onClickLabel = ...)` for better TalkBack experience.
- **Focus Management**: Use `FocusRequester` for complex forms.

## 5. Performance
- **Avoid heavy computations** in the Composable function body; use `LaunchedEffect` or `remember`.
- **Use `LazyColumn` / `LazyRow`** for large lists.
- **Profile** using the Layout Inspector and Macrobenchmark.
