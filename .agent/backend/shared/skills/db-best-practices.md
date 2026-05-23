# NestJS Database & ORM Best Practices

## 1. Avoid N+1 Queries
**Impact: HIGH**
Use eager loading (`relations`), joins, or DataLoader to avoid multiple queries when fetching related data.

## 2. Use Database Migrations
**Impact: HIGH**
Never use `synchronize: true` in production. Use migrations for all schema changes to maintain version control and safety.

## 3. Transactions for Multi-Step Operations
**Impact: HIGH**
Wrap related database changes in a transaction to ensure atomicity and data consistency.

## 4. Proper Indexing
**Impact: HIGH**
Ensure all columns used in `WHERE`, `ORDER BY`, or `JOIN` clauses are properly indexed.
