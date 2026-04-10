# NestJS Anti-Hallucination Rules for Cursor & Claude Code

> Stop your AI from generating deprecated APIs, phantom imports, and broken NestJS patterns.

**Free rule file** — drop it into your project and your AI coding assistant will stop making these common NestJS mistakes:

| Mistake | What AI Does Wrong | What This Rule Fixes |
|---------|-------------------|---------------------|
| Phantom imports | Imports `@nestjs/bull/decorators` (doesn't exist) | Enforces correct import paths |
| Deprecated APIs | Uses `getRepository()` (removed in TypeORM 0.3+) | Modern DI-based patterns |
| Express bleed-through | Returns via `@Res()` bypassing interceptors | NestJS-native response handling |
| Untyped code | `catch (error: any)` everywhere | `unknown` + type narrowing |
| Config leaks | `process.env.DB_URL` inline | `ConfigService.getOrThrow()` |
| SQL injection | String interpolation in queries | Parameterized queries only |
| Wrong decorators | `@Injectable()` on controllers, `@Body()` in GET | Correct decorator combinations |
| Unsafe DB config | `synchronize: true` in production | Blocked with explanation |

## Installation

### Cursor

```bash
mkdir -p .cursor/rules
curl -o .cursor/rules/anti-hallucination.mdc https://raw.githubusercontent.com/RolandiPkhakadze/nestjs-cursor-rules/anti-hallucination.mdc
```

### Claude Code

```bash
mkdir -p .claude/skills
curl -o .claude/skills/anti-hallucination.md https://raw.githubusercontent.com/RolandiPkhakadze/nestjs-cursor-rules/anti-hallucination.md
```

## What It Catches

### Banned imports that don't exist
```typescript
// ❌ AI generates these — they're not real
import { ... } from '@nestjs/bull/decorators';
import { ... } from '@nestjs/swagger/decorators';
import { ... } from '@nestjs/typeorm/repository';
import { ... } from 'nestjs-redis';

// ✅ Correct paths
import { InjectQueue, Processor, WorkerHost } from '@nestjs/bullmq';
import { ApiTags, ApiOperation } from '@nestjs/swagger';
import { InjectRepository } from '@nestjs/typeorm';
```

### Deprecated patterns
```typescript
// ❌ Removed in TypeORM 0.3+
const repo = getRepository(User);

// ✅ Modern pattern
@InjectRepository(User) private readonly userRepo: Repository<User>
```

### Type safety
```typescript
// ❌ AI loves this
catch (error: any) { ... }
const data: any = await response.json();

// ✅ What the rule enforces
catch (error: unknown) {
  if (error instanceof DomainError) { ... }
}
const data = await response.json() as PaymentResponse;
```

## Want More?

This is one rule from the **[NestJS Production Rules Pack](https://rolandipk.gumroad.com/l/nestjs-production-rules)** — 24 battle-tested rule files covering:

- 🏗️ **Core Architecture** — Module structure, DI patterns, domain errors, config validation, structured logging
- 🗄️ **Database** — TypeORM transactions, N+1 prevention, cursor pagination, migration safety
- 🔒 **Security** — JWT guards, permission-based RBAC, validation pipes, rate limiting
- 🧪 **Testing** — Unit/integration/e2e patterns, testcontainers, fixture factories
- ⚡ **Advanced Patterns** — BullMQ, Kafka, CQRS, saga orchestration, idempotency, multi-provider adapters

12 Cursor rules + 12 Claude Code skills. **[$29 on Gumroad →](https://rolandipk.gumroad.com/l/nestjs-production-rules)**

## License

MIT — use freely in any project. Attribution appreciated but not required.

## Contributing

Found a pattern the AI gets wrong? Open an issue or PR.
