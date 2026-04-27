# Fullstack Engineer Skill

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-orange)](https://claude.com/claude-code)
[![Stars](https://img.shields.io/github/stars/Wholorp/fullstack-engineer-skill?style=social)](https://github.com/Wholorp/fullstack-engineer-skill)

A **Claude Code** skill that turns Claude into a senior full-stack engineer. Every piece of code becomes clean, secure, fully typed, testable, and built on a foundation that scales.

---

## Why use it?

Without the skill, Claude writes code that works but isn't always production-ready. With the skill:

- **Clear project structure** — feature modules (router/controller/service/repository)
- **TypeScript strict mode** — no `any` without reason, types end-to-end
- **Consistent API design** — RESTful, unified envelope `{success, data}` / `{success, error}`, versioned from day one
- **Security baked in** — bcrypt 12+ rounds, helmet, rate limiting, scoped CORS, proper JWT
- **DB best practices** — always migrations, indexes on FKs, soft delete, pagination
- **Error handling** — `AppError` class + global middleware, no leaked stack traces
- **Frontend patterns** — Zustand for client state, TanStack Query for server state, React Hook Form + Zod
- **Clear workflow** — Schema → Contract → Scaffold → Bottom-up implementation

### The practical difference

| Aspect | Without skill | With skill |
|--------|---------------|------------|
| **CORS** | `app.use(cors())` open | scoped to specific origin |
| **Auth** | endpoint with no protection | rate limit + bcrypt + proper JWT |
| **Types** | `any` everywhere | shared types front/back |
| **Errors** | `console.log` | central logger + `AppError` |
| **DB** | hard delete + N+1 | soft delete + indexes + pagination |
| **Tests** | none | integration tests for happy + error paths |
| **Structure** | everything in `routes/` | independent feature modules |

---

## Before / After Example

### Request
> "Build me a login endpoint"

### Without the skill
```js
app.post('/login', async (req, res) => {
  const user = await db.query(`SELECT * FROM users WHERE email='${req.body.email}'`);
  if (user.password === req.body.password) {
    res.json({ token: jwt.sign({ id: user.id }, 'secret') });
  } else {
    res.status(401).json({ error: 'wrong' });
  }
});
```
**Issues**: SQL injection, plaintext password, hardcoded JWT secret, no rate limit, no validation, inconsistent error envelope.

### With the skill
```ts
// users.schema.ts
export const loginSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
});

// users.controller.ts
export const login = asyncHandler(async (req, res) => {
  const { email, password } = loginSchema.parse(req.body);
  const result = await usersService.login(email, password);
  res.json({ success: true, data: result });
});

// users.service.ts
export const login = async (email: string, password: string) => {
  const user = await usersRepo.findByEmail(email);
  if (!user) throw new AppError('AUTH_INVALID', 'Invalid credentials', 401);
  const ok = await bcrypt.compare(password, user.passwordHash);
  if (!ok) throw new AppError('AUTH_INVALID', 'Invalid credentials', 401);
  const token = jwt.sign({ id: user.id }, env.JWT_SECRET, { expiresIn: '15m' });
  return { token, user: { id: user.id, email: user.email } };
};

// router (with rateLimit middleware)
router.post('/login', authLimiter, login);
```

---

## Installation

### Per project
```bash
git clone https://github.com/Wholorp/fullstack-engineer-skill.git \
  .claude/skills/fullstack-engineer
```

### Globally (available across all your projects)
```bash
git clone https://github.com/Wholorp/fullstack-engineer-skill.git \
  ~/.claude/skills/fullstack-engineer
```

Final structure:
```
~/.claude/skills/fullstack-engineer/
├── SKILL.md
└── references/
    ├── backend-patterns.md
    ├── frontend-patterns.md
    └── security-checklist.md
```

---

## Usage

### Auto-trigger
The skill activates when you ask things like:

- "build a backend"
- "create an API for..."
- "scaffold a fullstack app"
- "design a database schema"
- "best practices for..."
- "build a system for..."
- "production-ready architecture"

### Explicit
```
/fullstack-engineer build me a task management system
```

### Full example
```
You: build me an API for user management
Claude: [activates the skill]
        1. Asks: what fields? what operations? who authenticates?
        2. Designs Prisma schema
        3. Defines API contract (TypeScript types)
        4. Builds bottom-up: repo → service → controller → router
        5. Adds Zod validation + AppError + tests
        6. Summarizes: what's built, what's missing, next step
```

---

## What's inside?

| File | Content |
|------|---------|
| `SKILL.md` | Philosophy, default stack, project structure, code standards, workflow |
| `references/backend-patterns.md` | CQRS, queues, caching, advanced patterns |
| `references/frontend-patterns.md` | Optimistic updates, infinite scroll, code splitting, PWA |
| `references/security-checklist.md` | Full pre-launch security checklist |

---

## Default Stack

| Layer | Technology |
|-------|------------|
| **Backend Runtime** | Node.js + TypeScript (or Python FastAPI) |
| **Framework** | Express / Fastify / FastAPI |
| **Database** | PostgreSQL or MongoDB |
| **ORM** | Prisma (TS) or SQLAlchemy (Py) |
| **Auth** | JWT + refresh tokens |
| **Validation** | Zod (TS) or Pydantic (Py) |
| **Frontend** | React + TypeScript (Next.js for SSR) |
| **Styling** | Tailwind CSS |
| **State** | Zustand + TanStack Query |
| **Forms** | React Hook Form + Zod |
| **Testing** | Vitest + Supertest + RTL |
| **DevOps** | Docker + GitHub Actions |

---

## When to use it

Good fit:
- New project from scratch
- Refactor of an existing project
- Code review
- Teaching best practices
- Systems that need to scale over time

Not a good fit:
- Quick one-off scripts (over-engineering)
- Throwaway prototypes
- Simple HTML files

---

## Contributing

PRs are welcome. If you have a pattern that fits, open an issue or a PR.

---

## License

[MIT](LICENSE) — use it, modify it, share it.

---

<div align="center">

Built for [Claude Code](https://claude.com/claude-code) with Claude

</div>
