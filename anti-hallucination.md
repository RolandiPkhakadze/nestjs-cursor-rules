# Skill: NestJS Anti-Hallucination

## When to use
ALWAYS active. Check every line of generated NestJS code against these rules.

## Banned Imports — These Don't Exist
```
❌ @nestjs/core/decorators
❌ @nestjs/swagger/decorators    → import from @nestjs/swagger
❌ @nestjs/typeorm/repository    → use @InjectRepository from @nestjs/typeorm
❌ @nestjs/passport/strategies   → import from passport-jwt, passport-local
❌ @nestjs/bull/decorators        → use @nestjs/bullmq (bull is legacy)
❌ nestjs-redis                   → use @nestjs-modules/ioredis or ioredis
❌ @nestjs/cqrs/decorators        → import from @nestjs/cqrs
❌ nestjs-config                  → use @nestjs/config
```

## Deprecated Patterns — NEVER Generate
- `getRepository()` / `getConnection()` — removed in TypeORM 0.3+, use `@InjectRepository()` DI
- `@nestjs/bull` with `@Process` decorator — use `@nestjs/bullmq` with `WorkerHost`
- `@Res()` without `{ passthrough: true }` — bypasses interceptors/serialization
- `synchronize: true` in production config — auto-generates destructive DDL

## Wrong Decorator Combinations
- `@Injectable()` on a controller — controllers use `@Controller()` only
- `@Body()` in a GET handler — GET requests should not have a body
- Both `@Param` and `@Query` with the same name

## Type Safety — Mandatory
- NEVER generate `any` — use `unknown` for catches, explicit types everywhere
- NEVER generate untyped event payloads — always use typed event classes
- NEVER generate untyped Map/Set — always `Map<string, TypedValue>`

## Configuration Safety
- NEVER use `process.env` directly — use `ConfigService.getOrThrow<T>()`
- NEVER hardcode connection strings, API keys, ports, or feature flags
- `ConfigService.get()` returns `undefined` silently — always use `getOrThrow()`

## Database Safety
- NEVER use string interpolation in SQL — always parameterized: `query('... WHERE id = $1', [id])`
- NEVER `synchronize: true` in production
- NEVER `DROP TABLE` or `TRUNCATE` in migration files without explicit comment

## Async Module Pitfalls
- `forRootAsync` factories must have `inject` array matching the factory params
- `imports` must include the modules providing injected services (e.g., `ConfigModule`)

## Before Generating
If unsure whether a package exists, DO NOT import it. Generate LESS code with correct patterns rather than MORE code with guesses.
