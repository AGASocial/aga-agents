# NestJS Testing Best Practices

## 1. Use Supertest for E2E
**Impact: HIGH**
Validate full request/response cycles including guards, pipes, and interceptors using E2E tests.

## 2. Mock External Services
**Impact: HIGH**
Never hit real external APIs or databases in unit tests. Mock dependencies to ensure tests are fast and deterministic.

## 3. Isolated Testing Modules
**Impact: HIGH**
Use `Test.createTestingModule` to create isolated environments for services and controllers.

## 4. Test Edge Cases
**Impact: MEDIUM**
Explicitly test error scenarios, timeouts, and validation failures.
