# iOS Performance Best Practices

## General Guidelines
1. **Main Thread**: Never perform heavy computations or synchronous network calls on the main thread. Dispatch them to background queues.
2. **Memory Management**: Avoid retain cycles by properly using `weak` and `unowned` references in closures and delegate patterns. Use Instruments to profile for memory leaks.
3. **UI Rendering**: Minimize the use of complex view hierarchies. In SwiftUI, avoid unnecessary state updates that trigger full re-renders. Use `LazyVStack` and `LazyHStack` for large lists.
4. **Image Loading**: Cache downloaded images. Downsample large images before displaying them to save memory.
5. **App Launch Time**: Defer non-critical initialization until after the app has fully launched.
