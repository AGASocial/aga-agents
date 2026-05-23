# Security Best Practices

## 1. XSS Prevention
**Impact: CRITICAL**

Angular auto-escapes template interpolation (`{{ }}`), property bindings (`[prop]`), and attribute bindings. Never bypass this.

```typescript
// FORBIDDEN — raw HTML from AI or user input
this.safeHtml = this.sanitizer.bypassSecurityTrustHtml(aiGeneratedText);

// ALLOWED — only for server-controlled, pre-sanitized strings
const serverCleaned = serverSanitize(html); // backend responsibility
this.safeHtml = this.sanitizer.bypassSecurityTrustHtml(serverCleaned);

// PREFERRED for AI-generated text — render as plain text
// template: <p>{{ prospect.insight.hypothesis }}</p>
```

If markdown rendering is required (e.g., AI insight cards), use a sanitizing parser (e.g., `marked` + `DOMPurify`), never raw `innerHTML`.

## 2. Authentication & Token Storage
**Impact: CRITICAL**

```typescript
@Injectable({ providedIn: 'root' })
export class AuthService {
  // Memory-only — cleared on page refresh (this is the desired behavior for access tokens)
  private readonly _accessToken = signal<string | null>(null);
  readonly accessToken = this._accessToken.asReadonly();
  readonly isAuthenticated = computed(() => this._accessToken() !== null);

  setToken(token: string): void { this._accessToken.set(token); }
  clearToken(): void { this._accessToken.set(null); }

  logout(): void {
    this.clearToken();
    this.http.post('/api/auth/logout', {}).subscribe(); // revoke refresh token cookie
    this.router.navigate(['/login']);
  }
}
```

Refresh tokens are `httpOnly` cookies — managed entirely by the backend. The Angular app never reads them.

## 3. Route Protection
**Impact: CRITICAL**

Every authenticated route must declare `canActivate: [authGuard]`. Never rely on UI-hiding alone — the guard must prevent the route from activating.

## 4. Secrets in the Bundle
**Impact: CRITICAL**

- `environment.ts` must not contain: API keys, client secrets, database connection strings.
- Allowed in `environment.ts`: `apiBaseUrl`, `sentryDsn` (public DSN), feature flags.
- All Exa, Firecrawl, and OpenAI API calls are made from the NestJS backend only. The Angular app calls `/api/...` exclusively.

## 5. CSRF Protection
**Impact: HIGH**

If cookie-based auth is used, configure CSRF protection:

```typescript
// app.config.ts — adds X-XSRF-TOKEN header automatically
importProvidersFrom(HttpClientXsrfModule.withOptions({
  cookieName: 'XSRF-TOKEN',
  headerName: 'X-XSRF-TOKEN',
}))
```

The backend (NestJS) must set the `XSRF-TOKEN` cookie and validate the header.

## 6. Content Security Policy

Work with the backend team to set:
```
Content-Security-Policy:
  default-src 'self';
  script-src 'self';
  style-src 'self' 'unsafe-inline';  // unsafe-inline needed for Angular styles — minimize
  img-src 'self' data: https:;
  connect-src 'self' https://o*.ingest.sentry.io;
  frame-ancestors 'none';
```

No CDN scripts. No `unsafe-eval`.

## 7. Dependency Audit

```bash
npm audit --audit-level=high
```

Run in CI. HIGH and CRITICAL vulnerabilities block the build.

## 8. Error Message Safety

Never surface internal error details (stack traces, SQL, internal service names) in user-visible messages. The `errorInterceptor` maps all errors to a safe `ApiError.message` string before they reach the component.
