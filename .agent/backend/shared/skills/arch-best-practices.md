# NestJS Architecture Best Practices

## 1. Avoid Circular Dependencies
**Impact: CRITICAL**
Circular dependencies occur when Module A imports Module B, and Module B imports Module A. This is the #1 cause of runtime crashes in NestJS.

### Correct (Extract shared logic or use events):
```typescript
// Option 1: Extract shared logic to a third module
@Module({
  providers: [SharedService],
  exports: [SharedService],
})
export class SharedModule {}

// Option 2: Use events for decoupled communication
@Injectable()
export class UsersService {
  constructor(private eventEmitter: EventEmitter2) {}
  async createUser(data: any) {
    this.eventEmitter.emit('user.created', user);
  }
}
```

## 2. Organize by Feature Modules
**Impact: CRITICAL**
Organize by feature (e.g., `users/`, `orders/`) rather than technical layer (e.g., `controllers/`, `services/`). Each module should be self-contained.

## 3. Proper Module Sharing Patterns
**Impact: CRITICAL**
NestJS modules are singletons. Export services explicitly and import the module where needed. Avoid providing the same service in multiple modules.

## 4. Single Responsibility for Services
**Impact: CRITICAL**
Each service should have one well-defined responsibility. Avoid "god services."

## 5. Use Repository Pattern for Data Access
**Impact: HIGH**
Encapsulate database logic in custom repositories to decouple business logic from the persistence layer.
