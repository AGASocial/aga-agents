# NestJS Error Handling Best Practices

## 1. Handle Async Errors Properly
**Impact: HIGH**
Always await promises or use `.catch()` to prevent unhandled rejections from crashing the process.

## 2. Throw Exceptions from Services
**Impact: HIGH**
Throw `HttpException` subclasses (or domain exceptions mapped to them) directly from services to keep controllers thin.

## 3. Use Exception Filters
**Impact: HIGH**
Centralize error formatting and logging using exception filters instead of manual try-catch in controllers.

## 4. Custom Exceptions
**Impact: MEDIUM**
Create descriptive custom exceptions that carry business-specific error codes and metadata.
