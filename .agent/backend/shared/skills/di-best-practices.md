# NestJS Dependency Injection Best Practices

## 1. Prefer Constructor Injection
**Impact: CRITICAL**
Always use constructor injection over property injection to make dependencies explicit and improve testability.

## 2. Avoid Service Locator Anti-Pattern
**Impact: HIGH**
Avoid `ModuleRef.get()` or global containers. Use explicit dependencies in constructors.

## 3. Understand Provider Scopes
**Impact: CRITICAL**
Default to singleton scope. Use request-scoped providers only when necessary (e.g., accessing request context) as they affect performance.

## 4. Use Injection Tokens for Interfaces
**Impact: HIGH**
Since TypeScript interfaces are erased at runtime, use string tokens or symbols for interface-based injection.

## 5. Interface Segregation
**Impact: HIGH**
Keep interfaces small and focused. Consumers shouldn't depend on methods they don't use.
