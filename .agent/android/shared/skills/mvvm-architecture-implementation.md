# MVVM Architecture Implementation

This skill defines the standard architectural patterns for Android development using MVVM and Clean Architecture principles.

## Layer Overview

### 1. Data Layer (Repository)
Responsible for fetching and caching data from network or local database.
- **Repository**: Single source of truth for data.
- **Data Sources**: Retrofit services, Room DAOs.
- **Mappers**: Convert Data Models to Domain Models.

### 2. Domain Layer (Optional but recommended)
Contains business logic and use cases.
- **Models**: Plain Kotlin objects representing business entities.
- **Use Cases**: Encapsulate a single task (e.g., `GetLeadsUseCase`).

### 3. UI Layer (ViewModel)
Exposes state and handles user events.
- **ViewModel**: Uses `StateFlow` to expose UI state.
- **UI State**: Data class representing everything the UI needs to display.

## Implementation Pattern

### UI State
```kotlin
data class LeadsUiState(
    val leads: List<Lead> = emptyList(),
    val isLoading: Boolean = false,
    val error: String? = null
)
```

### ViewModel
```kotlin
@HiltViewModel
class LeadsViewModel @Inject constructor(
    private val getLeadsUseCase: GetLeadsUseCase
) : ViewModel() {

    private val _uiState = MutableStateFlow(LeadsUiState())
    val uiState: StateFlow<LeadsUiState> = _uiState.asStateFlow()

    init {
        loadLeads()
    }

    private fun loadLeads() {
        viewModelScope.launch {
            _uiState.update { it.copy(isLoading = true) }
            val result = getLeadsUseCase()
            _uiState.update { 
                it.copy(leads = result.data ?: emptyList(), isLoading = false) 
            }
        }
    }
}
```

## Best Practices
- **Never** leak UI components (Context, Views) into the ViewModel.
- **Use `update`** for StateFlow to ensure thread-safety.
- **Handle errors** gracefully in the UI state.
