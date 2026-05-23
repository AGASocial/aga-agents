# NestJS Microservices Best Practices

## 1. Comprehensive Health Checks
**Impact: HIGH**
Implement liveness and readiness probes using `@nestjs/terminus` to help orchestrators manage service lifecycle.

## 2. Correct Message Patterns
**Impact: MEDIUM**
Use `MessagePattern` for request-response and `EventPattern` for fire-and-forget communication.

## 3. Reliable Background Jobs
**Impact: HIGH**
Use `@nestjs/bullmq` for heavy background tasks to decouple them from the request cycle and enable retry logic.

## 4. Handle Dead Letter Queues
**Impact: MEDIUM**
Implement dead letter queues for failed messages to ensure they are processed or reviewed later.
