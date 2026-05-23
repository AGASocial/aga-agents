# FE Deployment & Environments

Environment configuration for `apps/web`. **Current scope: local development only.** Cloud hosting (Vercel, Netlify, etc.) is documented here as a placeholder for a later phase.

## Environment files

| File | Used when | `production` | `apiBaseUrl` (local) |
|------|-----------|--------------|----------------------|
| `src/environments/environment.development.ts` | `ng serve`, `ng build --configuration development` | `false` | `'/api'` (proxied) |
| `src/environments/environment.ts` | `ng build` (production) | `true` | `'/api'` until deploy target is chosen |

Production `apiBaseUrl` will change when a hosted API URL exists (e.g. `https://api.example.com`). Do not put secrets in either file.

## ENVIRONMENT injection token

**Rule:** Services and stores inject `ENVIRONMENT`; they do not import `environment` directly.

```typescript
// src/app/core/tokens/environment.token.ts
export interface Environment {
  production: boolean;
  apiBaseUrl: string;
  sentryDsn: string; // public DSN only; empty until Sentry is configured
}

export const ENVIRONMENT = new InjectionToken<Environment>('environment');
```

Registered in `app.config.ts`:

```typescript
import { environment } from '../environments/environment.development';

{ provide: ENVIRONMENT, useValue: environment }
```

Production builds replace `environment.development.ts` with `environment.ts` via `angular.json` → `fileReplacements`.

## Local development (today)

```
┌─────────────────┐     /api/*      ┌──────────────────┐
│  Angular :4200  │ ──────────────► │  NestJS :3000    │
│  (ng serve)     │   proxy.conf    │  (start:dev)     │
└─────────────────┘                 └──────────────────┘
```

1. Start backend: `cd apps/backend && npm run start:dev`
2. Start web: `cd apps/web && npm start`
3. HTTP from app code: `HttpClient.get(\`${env.apiBaseUrl}/...\`)` → `/api/...` → backend

No `.env` file is required for the Angular app in local mode. Backend may use `.env` per NestJS conventions (never commit secrets).

## Secrets policy

| Allowed in Angular repo | Forbidden in Angular bundle |
|-------------------------|----------------------------|
| Public `sentryDsn` (when enabled) | API keys (Exa, Firecrawl, OpenAI) |
| `apiBaseUrl` path or public URL | Refresh tokens in `localStorage` |
| Feature flags (non-secret) | Private backend credentials |

All AI and scraping calls go through **NestJS** — see [security-best-practices.md](./security-best-practices.md).

## Testing with environments

In unit tests, provide a stub:

```typescript
providers: [
  {
    provide: ENVIRONMENT,
    useValue: { production: false, apiBaseUrl: '/api', sentryDsn: '' },
  },
]
```

## Future: hosted environments (not implemented)

When the team adds cloud deploy, extend this skill with:

| Environment | Branch / trigger | `apiBaseUrl` source |
|-------------|------------------|---------------------|
| Preview | PR | CI variable or preview API URL |
| Production | `main` | Production API URL |

Document the chosen platform in `apps/web/README.md` and add deploy steps to [fe-ci-cd.md](./fe-ci-cd.md).

## Related skills

- [fe-project-setup.md](./fe-project-setup.md) — two-terminal local run
- [http-api.md](./http-api.md) — HttpClient and interceptors
- [api-contract-workflow.md](./api-contract-workflow.md) — backend contract
