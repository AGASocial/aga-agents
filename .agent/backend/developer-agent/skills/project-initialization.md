# Backend Project Initialization & Bootstrapping

This skill describes the initial configuration, bootstrapping, and local run workflow for the NestJS backend application at `apps/backend`.

## Bootstrapping NestJS (main.ts)

When bootstrapping the NestJS backend application in `apps/backend/src/main.ts`, ensure that CORS is properly configured to allow requests from the frontend development server (`http://localhost:4200`). This is a critical development requirement to prevent CORS validation errors.

### Required CORS Setup

Ensure that `main.ts` includes the CORS configuration using `app.enableCors()` with the allowed origin:

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  
  // Enable CORS for frontend development (allow requests from http://localhost:4200)
  app.enableCors({
    origin: 'http://localhost:4200',
    credentials: true,
  });

  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

> **Note:** Alternatively, this can be written using a helper function or custom `addCors('http://localhost:4200')` mechanism to represent enabling CORS for the frontend origin.

## Run Locally

To run the backend application locally:

```bash
cd apps/backend
npm install
npm run start:dev
```

- Listens on **http://localhost:3000**
- Smoke test: `curl http://localhost:3000`
