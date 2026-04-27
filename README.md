<div dir="rtl">

# Fullstack Engineer Skill

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-orange)](https://claude.com/claude-code)
[![Stars](https://img.shields.io/github/stars/Wholorp/fullstack-engineer-skill?style=social)](https://github.com/Wholorp/fullstack-engineer-skill)

سكِل (Skill) لـ **Claude Code** يحوّل كلود لمهندس Full-Stack محترف. يضمن إن أي كود تكتبه أو تطلبه يكون نظيف، آمن، مكتوب بـTypes، قابل للاختبار، ومبني على أساس صلب.

---

## ايش الفائدة؟

بدون السكل، كلود يكتب كود يشتغل بس مو دايم production-ready. مع السكل:

- **بنية مشروع واضحة** — modules مفصولة (router/controller/service/repository)
- **TypeScript strict mode** — لا `any` بدون سبب، types end-to-end
- **API design موحّد** — RESTful، envelope ثابت `{success, data}` / `{success, error}`، versioning من اليوم الأول
- **أمان built-in** — bcrypt 12+ rounds, helmet, rate limit, CORS محدّد, JWT صحيح
- **DB best practices** — migrations دائماً، indexes على FKs، soft delete، pagination
- **Error handling** — `AppError` class + global middleware، ما يسرّب stack traces
- **Frontend patterns** — Zustand للـclient state, TanStack Query للـserver state, React Hook Form + Zod
- **Workflow واضح** — Schema → Contract → Scaffold → Bottom-up implementation

### الفرق العملي

| الجانب | بدون السكل | مع السكل |
|--------|-----------|----------|
| **CORS** | `app.use(cors())` مفتوح | مقيّد بـorigin محدّد |
| **Auth** | endpoint بدون حماية | rate limit + bcrypt + JWT صحيح |
| **Types** | `any` في كل مكان | shared types بين front/back |
| **Errors** | `console.log` | Logger مركزي + `AppError` |
| **DB** | hard delete + N+1 | soft delete + indexes + pagination |
| **Tests** | لا شيء | integration tests للـhappy + error |
| **Structure** | كل شي في `routes/` | feature modules مستقلة |

---

## مثال قبل/بعد

### الطلب
> "ابني لي endpoint لتسجيل الدخول"

### بدون السكل
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
**مشاكل**: SQL injection، password بدون hash، JWT secret hardcoded، لا rate limit، لا validation، error envelope غير موحّد.

### مع السكل
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

## التثبيت

### المشروع الواحد
```bash
git clone https://github.com/Wholorp/fullstack-engineer-skill.git \
  .claude/skills/fullstack-engineer
```

### عام (متاح لكل مشاريعك)
```bash
git clone https://github.com/Wholorp/fullstack-engineer-skill.git \
  ~/.claude/skills/fullstack-engineer
```

البنية النهائية:
```
~/.claude/skills/fullstack-engineer/
├── SKILL.md
└── references/
    ├── backend-patterns.md
    ├── frontend-patterns.md
    └── security-checklist.md
```

---

## الاستعمال

### تفعيل تلقائي
السكل يفعّل لما تطلب:

| العربي | English |
|--------|---------|
| "ابني لي backend" | "build a backend" |
| "اعمل API لـ..." | "create an API for..." |
| "ابغا مشروع كامل" | "scaffold a fullstack app" |
| "design a database schema" | "design DB schema" |
| "best practices لـ..." | "best practices for..." |
| "ابني نظام..." | "build a system for..." |

### تفعيل صريح
```
/fullstack-engineer ابني لي نظام مهام
```

### مثال كامل
```
انت: ابني لي API لإدارة المستخدمين
كلود: [يفعّل السكل]
       1. يسأل: ايش الحقول؟ ايش العمليات؟ من يصادق؟
       2. يصمم Prisma schema
       3. يعرّف API contract (TypeScript types)
       4. يبني bottom-up: repo → service → controller → router
       5. يضيف Zod validation + AppError + tests
       6. يلخّص: ايش بنى، ايش ناقص، الخطوة الجاية
```

---

## ايش جوّاه؟

| الملف | المحتوى |
|------|---------|
| `SKILL.md` | الفلسفة، stack الافتراضي، بنية المشروع، معايير الكود، workflow |
| `references/backend-patterns.md` | CQRS, queues, caching, advanced patterns |
| `references/frontend-patterns.md` | optimistic updates, infinite scroll, code splitting, PWA |
| `references/security-checklist.md` | checklist كاملة قبل launch |

---

## Stack الافتراضي

| الطبقة | التقنية |
|--------|---------|
| **Backend Runtime** | Node.js + TypeScript (أو Python FastAPI) |
| **Framework** | Express / Fastify / FastAPI |
| **Database** | PostgreSQL أو MongoDB |
| **ORM** | Prisma (TS) أو SQLAlchemy (Py) |
| **Auth** | JWT + refresh tokens |
| **Validation** | Zod (TS) أو Pydantic (Py) |
| **Frontend** | React + TypeScript (Next.js للـSSR) |
| **Styling** | Tailwind CSS |
| **State** | Zustand + TanStack Query |
| **Forms** | React Hook Form + Zod |
| **Testing** | Vitest + Supertest + RTL |
| **DevOps** | Docker + GitHub Actions |

---

## متى تستعمله؟

✅ مشروع جديد من الصفر
✅ refactor لمشروع قائم
✅ مراجعة كود (code review)
✅ تعليم best practices
✅ نظام يحتاج يكبر مع الوقت

❌ سكربت سريع لمرة واحدة (over-engineering)
❌ prototype للتجربة فقط
❌ ملف HTML بسيط

---

## المساهمة

PRs مرحّب بها. إذا عندك pattern تحس ينفع، افتح issue أو PR.

---

## الترخيص

[MIT](LICENSE) — استخدمه، عدّله، شاركه.

---

<div align="center">

صُنع لـ [Claude Code](https://claude.com/claude-code) بـ Claude

</div>

</div>
