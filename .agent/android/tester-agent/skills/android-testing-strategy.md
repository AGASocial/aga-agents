# Android Testing Strategy

This skill outlines the strategy and tools for testing Android applications.

## 1. Unit Testing (JUnit 5 + MockK)
Test business logic in ViewModels and Use Cases.
- **Location**: `src/test/java`
- **Goal**: Fast, isolated tests.

```kotlin
class MainViewModelTest {
    private val getLeadsUseCase = mockk<GetLeadsUseCase>()
    private lateinit var viewModel: MainViewModel

    @Test
    fun `loadLeads should update UI state with success`() = runTest {
        coEvery { getLeadsUseCase() } returns Result.Success(listOf(Lead("1")))
        viewModel = MainViewModel(getLeadsUseCase)

        viewModel.uiState.test {
            val state = awaitItem()
            assertTrue(state.leads.isNotEmpty())
        }
    }
}
```

## 2. UI Testing (Compose Test Rule)
Test UI components and user flows.
- **Location**: `src/androidTest/java`
- **Goal**: Verify UI behavior and accessibility.

```kotlin
class SearchScreenTest {
    @get:Rule
    val composeTestRule = createComposeRule()

    @Test
    fun searchField_isDisplayed() {
        composeTestRule.setContent {
            SearchScreen()
        }
        composeTestRule.onNodeWithTag("search_field").assertIsDisplayed()
    }
}
```

## 3. Integration Testing
Test interaction between components (e.g., Room + Repository).

## Best Practices
- **Use Turbine** for testing `Flow` and `StateFlow`.
- **Avoid `Thread.sleep`**; use `advanceTimeBy` or `awaitItem`.
- **Test the "Happy Path"** and "Error Path".
- **Keep tests hermetic** (no real network or database if possible).
