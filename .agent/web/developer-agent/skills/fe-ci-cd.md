# FE CI/CD

Continuous integration for `apps/web`. **Deployment is local-only for now**; this skill covers automated checks on pull requests.

## Workflow file

Path: `.github/workflows/web.yml`

Triggers on changes under `apps/web/` or the workflow file itself.

## Pipeline steps

| Step | Command | Purpose |
|------|---------|---------|
| Install | `npm ci` | Reproducible install (`apps/web/package-lock.json`) |
| Typecheck | `npm run typecheck` | `tsc -p tsconfig.app.json --noEmit` |
| Test | `npm run test:ci` | Karma + ChromeHeadless |
| Build | `npm run build` | Production Angular build + budgets |

Working directory for all steps: **`apps/web`**.

## Local parity

Run the same checks before opening a PR:

```bash
cd apps/web
npm ci
npm run typecheck
npm run test:ci
npm run build
```

## Node version

Use **Node 22** in CI (match team local setup). Pin in workflow with `actions/setup-node@v4`.

## When CI fails

| Failure | Likely fix |
|---------|------------|
| typecheck | Fix strict TypeScript errors; no `any` |
| test:ci | Update specs; provide `ENVIRONMENT` in TestBed |
| build | Bundle over budget — lazy-load features, reduce imports |
| npm ci | Commit `package-lock.json` changes |

## Lint (future)

When `@angular-eslint` is added to `apps/web`, append `npm run lint` to the workflow and to the local pre-PR checklist.

## Related skills

- [fe-project-setup.md](./fe-project-setup.md) — local run and scaffold
- [web-code-review.md](./web-code-review.md) — merge standards
