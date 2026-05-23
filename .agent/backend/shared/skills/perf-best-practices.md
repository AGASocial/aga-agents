# NestJS Performance Best Practices

## 1. Async Lifecycle Hooks
**Impact: HIGH**
Return promises from hooks like `onModuleInit` to ensure the application waits for initialization but doesn't block synchronously.

## 2. Lazy Loading Modules
**Impact: MEDIUM**
Use `LazyModuleLoader` for rarely-used or heavy features to improve application startup time, especially in serverless environments.

## 3. Optimize Database Queries
**Impact: HIGH**
Select only required columns, use indexes, and avoid over-fetching relations. Database latency is usually the bottleneck.

## 4. Strategic Caching
**Impact: HIGH**
Use `CacheModule` with appropriate TTLs for expensive operations and frequently accessed data. Implement cache invalidation on updates.
