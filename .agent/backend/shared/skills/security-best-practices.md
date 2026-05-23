# NestJS Security Best Practices

## 1. Secure JWT Implementation
**Impact: CRITICAL**
Use `@nestjs/jwt` with `@nestjs/passport`. Never store sensitive data in payloads. Use short-lived access tokens and secure refresh tokens.

## 2. Implement Rate Limiting
**Impact: HIGH**
Use `@nestjs/throttler` to prevent brute-force attacks and abuse, especially on auth and heavy endpoints.

## 3. Validate All Input
**Impact: HIGH**
Mandatory use of `ValidationPipe` with `class-validator` on DTOs. Never trust user-provided data.

## 4. Use Guards for Access Control
**Impact: HIGH**
Enforce authentication and authorization using Guards. Use declarative decorators (e.g., `@Roles()`) for clarity.

## 5. Sanitize Output (XSS Prevention)
**Impact: HIGH**
Sanitize user-generated content and set proper security headers (e.g., using `helmet`).
