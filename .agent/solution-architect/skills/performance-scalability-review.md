# Performance & Scalability Review

Use when the user asks for a performance review, scalability audit, load readiness assessment, bottleneck analysis, or capacity planning for a Next.js application and its backend APIs.

**Target:** Support at least **10,000 concurrent users** without service degradation.

**Related skills:**
- [web-perf-best-practices.md](../../web/shared/skills/web-perf-best-practices.md)
- [perf-best-practices.md](../../backend/shared/skills/perf-best-practices.md)
- [db-best-practices.md](../../backend/shared/skills/db-best-practices.md)
- [api-best-practices.md](../../backend/shared/skills/api-best-practices.md)
- [arch-best-practices.md](../../backend/shared/skills/arch-best-practices.md)

---

## Role

You are a Senior Performance Engineer, Distributed Systems Architect, and Next.js expert.

Perform a comprehensive performance and scalability review of my application, focusing on the interaction between the Next.js frontend and the backend APIs.

Your objective is to identify bottlenecks, scalability risks, architectural weaknesses, inefficient API usage patterns, and any design decisions that could cause performance degradation, increased latency, excessive infrastructure costs, or service outages when the system scales from hundreds to thousands or tens of thousands of concurrent users.

---

## Review Workflow

Copy this checklist and track progress:

```
Review Progress:
- [ ] Map frontend routes, rendering strategies, and data-fetching patterns
- [ ] Map all frontend-to-backend API calls (endpoints, frequency, sequencing)
- [ ] Trace backend endpoints to database queries and external dependencies
- [ ] Analyze network payloads, caching layers, and CDN usage
- [ ] Model behavior at 1K / 5K / 10K / 50K concurrent users
- [ ] Document every issue with severity, root cause, and recommended fix
- [ ] Produce all six deliverables (see Deliverables section)
```

**Step 1 — Discovery:** Read project structure, routing, data-fetching utilities, API clients, backend route handlers/controllers, ORM/query layers, and infrastructure config (CDN, caching, queues, rate limits).

**Step 2 — Frontend analysis:** Walk each critical user path. Note rendering mode per page, request waterfalls, duplicate calls, bundle composition, and hydration boundaries.

**Step 3 — API mapping:** Build a call graph from UI action → API endpoint → DB/external service. Flag sequential chains, N+1 patterns, over-fetching, and chatty polling.

**Step 4 — Backend & database:** Profile hot endpoints for blocking I/O, missing indexes, connection pool sizing, and cache hit rates.

**Step 5 — Load modeling:** Estimate failure points at each concurrency tier. Identify cascading failure paths.

**Step 6 — Report:** Rank findings by risk. Score readiness. Produce roadmap, load-test plan, capacity estimate, and monitoring list.

Be extremely critical. Do not assume current implementations are optimal. Look for hidden bottlenecks, architectural anti-patterns, and scaling risks.

---

## Analysis Perspectives

### 1. Frontend (Next.js)

- Review page rendering strategies (SSR, SSG, ISR, CSR, React Server Components).
- Detect unnecessary re-renders.
- Identify waterfall API calls.
- Detect duplicate API requests.
- Review client-side state management.
- Analyze loading strategies.
- Review caching opportunities.
- Identify hydration bottlenecks.
- Analyze bundle sizes and code-splitting opportunities.
- Detect components that may cause performance issues under heavy traffic.

### 2. API Consumption Patterns

- Map all frontend-to-backend API calls.
- Identify sequential requests that could be parallelized.
- Detect over-fetching and under-fetching.
- Review pagination strategies.
- Analyze request frequency.
- Detect chatty API patterns.
- Review polling implementations.
- Identify N+1 request patterns.
- Detect unnecessary retries.
- Analyze timeout configurations.

### 3. Backend Scalability

- Review endpoint design.
- Analyze request throughput limits.
- Identify endpoints likely to become hotspots under load.
- Review synchronous vs asynchronous operations.
- Detect blocking operations.
- Identify expensive database queries.
- Review caching strategies.
- Analyze queue usage.
- Review horizontal scaling readiness.
- Detect shared resources that may become bottlenecks.

### 4. Database Layer

- Review query patterns.
- Detect N+1 database queries.
- Identify missing indexes.
- Analyze joins and aggregations.
- Review transaction usage.
- Identify locking risks.
- Detect potential connection pool exhaustion.
- Review read/write scalability concerns.

### 5. Network and Infrastructure

- Analyze payload sizes.
- Review compression opportunities.
- Review CDN usage.
- Detect excessive round trips.
- Analyze latency-sensitive paths.
- Review API Gateway and Load Balancer considerations.
- Identify rate-limiting requirements.

### 6. Reliability Under Load

Estimate behavior under:

- 1,000 concurrent users
- 5,000 concurrent users
- 10,000 concurrent users
- 50,000 concurrent users

Also review:

- Identify probable failure points.
- Describe cascading failure scenarios.
- Review timeout and retry strategies.
- Review circuit breaker opportunities.
- Analyze resilience patterns.

### 7. Security Impact on Performance

- Authentication overhead.
- Token validation frequency.
- Session management efficiency.
- Authorization bottlenecks.

---

## Issue Format

For every issue found, provide:

| Field | Content |
|---|---|
| **Severity** | Critical / High / Medium / Low |
| **Description** | What was observed |
| **Root Cause** | Why it exists |
| **Impact** | User-facing or operational effect |
| **Estimated Scalability Risk** | When and how it breaks under load |
| **Recommended Fix** | Concrete remediation |
| **Expected Performance Gain** | Quantified or directional improvement |

---

## Deliverables

Generate all of the following:

### 1. Bottleneck Report

Rank every issue by risk (Critical first). Group by layer: Frontend → API → Backend → Database → Network → Reliability → Security.

### 2. Scalability Readiness Score (0–100)

Provide an overall score with a breakdown per analysis area. Explain what prevents a higher score.

### 3. Performance Optimization Roadmap

Phased plan:

- **Phase 1 — Quick wins** (days): low-effort, high-impact fixes
- **Phase 2 — Structural improvements** (weeks): architectural changes
- **Phase 3 — Scale hardening** (months): infrastructure, sharding, caching layers

Each item: effort estimate, dependency, expected outcome.

### 4. Load Testing Strategy

- Tools (e.g., k6, Artillery, Locust)
- Scenarios to simulate (critical user journeys)
- Ramp profiles for 1K / 5K / 10K / 50K concurrent users
- Success criteria (p50/p95/p99 latency, error rate, throughput)
- Environment requirements (staging parity, data volume)

### 5. Capacity Planning Estimate

- Current estimated capacity
- Resources needed for 10K concurrent users (compute, DB connections, cache memory, bandwidth)
- Cost scaling trajectory
- First resource to saturate under load

### 6. Production Monitoring Metrics

List metrics that should be monitored in production, including:

- Frontend: LCP, INP, TTFB, JS bundle size, hydration time
- API: request rate, latency percentiles, error rate, timeout rate
- Backend: CPU/memory, queue depth, thread pool utilization
- Database: query latency, connection pool usage, slow query count, lock waits
- Infrastructure: CDN hit rate, cache hit rate, rate-limit rejections
