# FE Project Setup

Scaffold and run the Angular **21** web app at `apps/web` in the application monorepo. **Local development is the default** until cloud deployment is added.

## Repository layout

```
apps/web/                          # Angular application (project name: web-app)
├── angular.json
├── package.json
├── proxy.conf.json                # Local API proxy → NestJS backend
├── src/
│   ├── app/
│   │   ├── core/tokens/           # ENVIRONMENT injection token
│   │   ├── shared/                # Reusable UI (add as needed)
│   │   └── features/              # Feature modules (add per user story)
│   └── environments/
│       ├── environment.development.ts
│       └── environment.ts         # Used for production builds only
└── README.md                      # Human quick-start (keep in sync with this skill)
```

Future feature code follows [arch-best-practices.md](./arch-best-practices.md): `src/app/features/<feature-name>/`.

## First-time bootstrap

If `apps/web` does not exist yet:

```bash
cd /path/to/your-monorepo
npx -y @angular/cli@21 new web-app \
  --directory apps/web \
  --routing \
  --style scss \
  --strict \
  --standalone \
  --package-manager npm \
  --skip-git \
  --ssr=false \
  --defaults
```

Then apply the repo-specific pieces from this repo’s `apps/web`:

- `proxy.conf.json`
- `src/environments/*`
- `src/app/core/tokens/environment.token.ts`
- `angular.json` serve `proxyConfig` and production `fileReplacements`
- Performance budgets in `angular.json` (tighten toward ≤200 KB **gzip** transfer as features ship; starter raw budget is higher until lazy routes exist)

## Install dependencies

```bash
cd apps/web
npm install
```

## Run locally (required workflow)

Use **two terminals** — backend first, then web.

### 1. Backend (NestJS)

```bash
cd apps/backend
npm install
npm run start:dev
```

- Listens on **http://localhost:3000**
- Smoke test: `curl http://localhost:3000`

### 2. Web (Angular)

```bash
cd apps/web
npm install
npm start
```

- Dev server: **http://localhost:4200**
- Uses `environment.development.ts` and `proxy.conf.json`
- `npm start` runs `ng serve --configuration development`

### API proxy

The browser must call **`/api/...`**, not `http://localhost:3000` directly (avoids CORS during local dev).

| Browser request | Proxied to |
|-----------------|------------|
| `GET http://localhost:4200/api` | `GET http://localhost:3000/` |

Config: `apps/web/proxy.conf.json`.

Services use `inject(ENVIRONMENT).apiBaseUrl` (default `'/api'` in development).

## Verify the stack

1. Backend hello: `curl http://localhost:3000`
2. Open http://localhost:4200 — shell shows API base `/api`
3. Optional: `curl http://localhost:4200/api` (via proxy while `npm start` is running)

## Common commands

```bash
cd apps/web
npm run typecheck    # tsc, no emit
npm test             # Karma (watch)
npm run test:ci      # Headless, for CI
npm run build        # Production build → dist/web-app
```

## Agent checklist (new feature work)

Before implementing UI for a user story:

- [ ] Backend endpoint exists or is tracked in a paired backend task
- [ ] `npm start` works in `apps/web` with proxy
- [ ] Feature folder created under `src/app/features/<name>/`
- [ ] Types aligned with [api-contract-workflow.md](./api-contract-workflow.md)

## Related skills

- [fe-deployment-and-environments.md](./fe-deployment-and-environments.md) — env files and `ENVIRONMENT` token
- [fe-ci-cd.md](./fe-ci-cd.md) — GitHub Actions
- [feature-implementation-workflow.md](./feature-implementation-workflow.md) — per-feature build steps
