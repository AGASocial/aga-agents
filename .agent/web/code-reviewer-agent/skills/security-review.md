# Security Review Checklist

Run this checklist on every PR that touches: authentication, authorization, forms, API integration, AI-generated content rendering, or user-generated data.

---

## 1. Authentication & Token Handling — CRITICAL

- [ ] Access tokens stored only in memory (Angular signal in `AuthService`) — never `localStorage`, `sessionStorage`, or cookies accessible to JS.
- [ ] Refresh tokens are `httpOnly`, `Secure`, `SameSite=Strict` cookies set by the server.
- [ ] Token never logged to the console or included in error reports.
- [ ] `AuthService.logout()` clears the in-memory token AND calls the backend revoke endpoint.
- [ ] Token expiry is handled — expired tokens trigger re-authentication, not an infinite loop.

## 2. Route Authorization — CRITICAL

- [ ] Every route that requires a logged-in user has `canActivate: [authGuard]`.
- [ ] Role-restricted routes have a role guard (`canActivate: [roleGuard('ADMIN')]`).
- [ ] Guards redirect unauthorized users — they do not just return `false` (which shows a blank page).
- [ ] No sensitive data is fetched in a resolver before the auth guard passes.

## 3. XSS Prevention — CRITICAL

- [ ] No `[innerHTML]` binding on user-provided or AI-generated content.
- [ ] If HTML must be rendered (e.g., AI-generated insight text), it is sanitized server-side AND passed through `DomSanitizer.sanitize(SecurityContext.HTML, value)` — never `bypassSecurityTrustHtml` on dynamic content.
- [ ] No `eval()`, `new Function()`, or dynamic `<script>` injection anywhere.
- [ ] Template interpolation (`{{ }}`) used for all user-visible string values — Angular escapes these automatically.

## 4. Secrets & Environment — CRITICAL

- [ ] Zero API keys (Exa, Firecrawl, OpenAI, Sentry DSN write key) in the Angular bundle or `environment.ts`.
- [ ] `environment.ts` contains only: `apiBaseUrl`, `sentryDsn` (public), feature flags. Nothing secret.
- [ ] All third-party AI API calls proxied through the NestJS backend — the browser never calls Exa/Firecrawl directly.
- [ ] `ng build --source-map` disabled in production — source maps not publicly served.

## 5. Input Validation — HIGH

- [ ] All form fields have client-side `Validators` (UX).
- [ ] File uploads (if any) validate type and size client-side before upload.
- [ ] URL inputs are validated to reject `javascript:` and `data:` schemes.
- [ ] Client-side validation is defense-in-depth only — never relied on for security (backend re-validates everything).

## 6. CSRF Protection — HIGH

- [ ] If cookie-based auth is used, `HttpClientXsrfModule` is configured with the correct header and cookie names.
- [ ] `SameSite=Strict` or `SameSite=Lax` on all auth cookies (set by backend).

## 7. Content Security Policy — HIGH

- [ ] Backend sets a `Content-Security-Policy` header.
- [ ] No `unsafe-inline` in the `script-src` directive.
- [ ] Angular's trusted types policy is enforced where supported.
- [ ] Third-party CDN scripts (if any) use Subresource Integrity (`integrity` attribute).

## 8. Dependency Security — MEDIUM

- [ ] `npm audit` run — no HIGH or CRITICAL vulnerabilities in production dependencies.
- [ ] No packages with known supply-chain compromises.
- [ ] Major version pins in `package.json` — no `*` or overly broad ranges.

## 9. Error Messages — MEDIUM

- [ ] Error messages shown to users do not expose internal structure (stack traces, SQL, file paths).
- [ ] API error responses mapped to user-friendly messages in the service layer.
- [ ] Sentry captures the full technical error internally; the user sees only a safe message.

## 10. AI-Generated Content — MEDIUM (specific to this platform)

- [ ] Prospect names, signals, and insights rendered via template interpolation (`{{ }}`), never `innerHTML`.
- [ ] If markdown is rendered (e.g., insight text), use a sanitizing markdown renderer — not raw `innerHTML`.
- [ ] AI-generated outreach drafts treated as untrusted content — sanitized before display.
