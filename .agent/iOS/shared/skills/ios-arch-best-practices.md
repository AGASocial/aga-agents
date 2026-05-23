# iOS Architecture Best Practices

## General Guidelines
1. **Separation of Concerns**: Use an established architecture pattern such as MVVM, Clean Architecture, or VIPER to decouple UI from business logic.
2. **Dependency Injection**: Pass dependencies into components rather than relying on singletons or tight coupling.
3. **Modularization**: Break down large monolithic codebases into smaller, reusable modules (e.g., Swift Packages).
4. **Data Flow**: Ensure unidirectional data flow, especially when using SwiftUI (e.g., using `@State`, `@Binding`, and `@EnvironmentObject` correctly).
5. **Networking Layer**: Isolate API calls and data mapping behind protocols and service classes.
