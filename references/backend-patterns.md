# Backend Advanced Patterns

## Repository Pattern
Separate data access from business logic. The service never talks to the DB directly.

```ts
// users.repository.ts
export class UsersRepository {
  constructor(private db: PrismaClient) {}

  async findById(id: string) {
    return this.db.user.findUnique({ where: { id } });
  }

  async create(data: CreateUserInput) {
    return this.db.user.create({ data });
  }
}

// users.service.ts
export class UsersService {
  constructor(private repo: UsersRepository) {}

  async getUser(id: string) {
    const user = await this.repo.findById(id);
    if (!user) throw new AppError(404, 'USER_NOT_FOUND', 'User not found');
    return user;
  }
}
```

## Pagination Pattern
Always paginate lists. Use cursor-based pagination for large datasets, offset for small ones.

```ts
// Cursor-based (better performance at scale)
interface PaginatedResult<T> {
  data: T[];
  nextCursor: string | null;
  hasMore: boolean;
}

async function getUsers(cursor?: string, limit = 20): Promise<PaginatedResult<User>> {
  const users = await db.user.findMany({
    take: limit + 1,
    cursor: cursor ? { id: cursor } : undefined,
    orderBy: { createdAt: 'desc' },
  });

  const hasMore = users.length > limit;
  return {
    data: users.slice(0, limit),
    nextCursor: hasMore ? users[limit - 1].id : null,
    hasMore,
  };
}
```

## Queue Workers
For heavy operations (email, PDF generation, webhooks), push to a queue rather than blocking the request.

```ts
// Use BullMQ with Redis
import { Queue, Worker } from 'bullmq';

const emailQueue = new Queue('email', { connection: redisConnection });

// In your service:
await emailQueue.add('welcome-email', { userId, email });

// Worker (runs separately):
new Worker('email', async (job) => {
  const { userId, email } = job.data;
  await sendWelcomeEmail(email);
});
```

## Caching Strategy
Cache at the right level. Cache computed results, not raw DB rows (usually).

```ts
import { redis } from '../lib/redis';

async function getUserWithStats(userId: string) {
  const cacheKey = `user:stats:${userId}`;
  const cached = await redis.get(cacheKey);
  if (cached) return JSON.parse(cached);

  const data = await computeUserStats(userId); // expensive query
  await redis.setex(cacheKey, 300, JSON.stringify(data)); // 5 min TTL
  return data;
}
```

## Environment Config with Zod

```ts
// config/env.ts
import { z } from 'zod';

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']),
  PORT: z.coerce.number().default(3000),
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  REDIS_URL: z.string().optional(),
});

export const env = envSchema.parse(process.env);
// App crashes at startup if env is invalid — better than crashing mid-request
```

## Global Error Handler (Express)

```ts
// middleware/errorHandler.ts
import { Request, Response, NextFunction } from 'express';
import { AppError } from '../lib/AppError';
import { logger } from '../lib/logger';

export function errorHandler(err: Error, req: Request, res: Response, _next: NextFunction) {
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      success: false,
      error: { code: err.code, message: err.message },
    });
  }

  logger.error({ err, path: req.path, method: req.method }, 'Unhandled error');

  return res.status(500).json({
    success: false,
    error: { code: 'INTERNAL_ERROR', message: 'Something went wrong' },
  });
}
```
