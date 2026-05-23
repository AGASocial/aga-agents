# NestJS API Design Best Practices

## 1. Use Response DTOs
**Impact: MEDIUM**
Never return entity objects directly. Use DTOs with `class-transformer` to control data exposure and maintain a stable API contract.

## 2. Leverage Interceptors
**Impact: MEDIUM**
Centralize cross-cutting concerns like logging, response transformation, and performance timing in interceptors.

## 3. Implement API Versioning
**Impact: MEDIUM**
Use NestJS's built-in versioning (URI, header, or media type) to evolve your API without breaking existing clients.

## 4. Use Built-in Pipes
**Impact: MEDIUM**
Use standard pipes (`ParseIntPipe`, `ParseUUIDPipe`) for common parameter transformations instead of manual parsing.
