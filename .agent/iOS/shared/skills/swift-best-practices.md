# Swift Best Practices

## 1. Use Value Types
- Prefer **Structs** over Classes when possible (for data models, view models).
- Use **Enums** for state machines and status indicators.

## 2. Swift Concurrency
- Use **async/await** for asynchronous operations.
- Use **Task** to create and manage concurrent tasks.
- Use **Actors** for mutable shared state.
- Use **Sendable** protocol for thread safety.

## 3. Data Handling
- Use **Codable** for easy encoding/decoding of API responses.
- Use **guard** for early returns and cleaner error handling.
- Use **Optional Chaining** (`?.`) and **Nil-Coalescing** (`??`) operators.

## 4. UI Best Practices
- **SwiftUI:** Use **State**, **Binding**, and **EnvironmentObject** for state management.
- **UIKit:** Use **MVVM** architecture with ViewControllers observing ViewModels.
- Always handle **Loading**, **Error**, and **Empty** states in the UI.

## 5. Error Handling
- Use **Error Protocol** for custom error types.
- Use **do-catch** blocks for proper error propagation.
- Provide user-friendly error messages for end-users.
