# API Contract Workflow

The TypeScript interface is the contract. Frontend and backend must agree before implementation begins on either side.

## Step 1 — Check for an OpenAPI Spec

The NestJS backend should expose `/api-json` (Swagger JSON) in non-production environments.

```bash
curl http://localhost:3000/api-json -o openapi.json
npx openapi-typescript openapi.json -o src/app/core/api/api.types.ts
```

If this pipeline is set up, **never hand-write types that already exist in the generated file**.

## Step 2 — Hand-Write When Necessary

When the endpoint doesn't exist yet, define the contract in a shared location:

```
src/app/core/api/
├── api.types.ts          # Generated types (do not edit manually)
├── <domain>.models.ts    # Hand-written domain models until generation catches up
```

Example:
```typescript
// src/app/features/prospect-discovery/models/prospect.model.ts

export type ProspectScore = 'HOT' | 'WARM' | 'LOW';
export type ProspectStatus = 'NEW' | 'REVIEWED' | 'CONTACTED';

export interface Prospect {
  id: string;
  name: string;
  website: string;
  linkedIn?: string;
  industry: string;
  estimatedSize: string;
  score: ProspectScore;
  status: ProspectStatus;
  signals: Signal[];
  insights: Insight;
  createdAt: string; // ISO 8601
}

export interface Signal {
  type: 'HIRING' | 'SCALING' | 'CONTENT_GAP';
  description: string;
  rationale: string;
}

export interface Insight {
  hypothesis: string;
  painPoints: string[];
  consultingAngle: string;
}

export interface CreateProspectRunDto {
  clientProfileId: string;
}

export interface ProspectListResponse {
  items: Prospect[];
  total: number;
  page: number;
  pageSize: number;
}
```

## Step 3 — Backend Alignment

Share the interface file (or a PR link) with the backend developer before starting implementation. Both sides must agree on:
- Field names and casing (use `camelCase` on both sides)
- Optional vs. required fields
- Enum values
- Pagination envelope shape

## Step 4 — Runtime Validation (Optional but Recommended)

For responses from AI agents (non-deterministic shape), validate at runtime using `zod`:

```typescript
import { z } from 'zod';

const ProspectSchema = z.object({
  id: z.string().uuid(),
  name: z.string().min(1),
  score: z.enum(['HOT', 'WARM', 'LOW']),
  // ...
});

// In the service
getById(id: string): Observable<Prospect> {
  return this.http.get<unknown>(`/api/prospects/${id}`).pipe(
    map(raw => ProspectSchema.parse(raw)), // throws ZodError on mismatch
  );
}
```

## Step 5 — Versioning

If the backend introduces a breaking change to an existing endpoint:
1. Backend adds the new shape at a versioned path (e.g., `/api/v2/prospects`).
2. Frontend service is updated before the old version is removed.
3. Both changes ship in the same PR or coordinated PRs — never break the running app.
