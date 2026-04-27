<div dir="rtl">

# Fullstack Engineer Skill

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
| بدون | مع |
|------|-----|
| `app.use(cors())` مفتوح | CORS مقيّد بـorigin محدّد |
| Endpoints بدون rate limit | Auth endpoints محمية ضد brute-force |
| `any` types في كل مكان | Types مشتركة بين front/back |
| `console.log` للأخطاء | Logger مركزي + AppError |
| ميّزات بدون اختبار | Integration tests للـhappy path + error case |

---

## التثبيت

### المشروع الواحد
ضع المجلد في `.claude/skills/`:

```bash
git clone https://github.com/Wholorp/fullstack-engineer-skill.git \
  .claude/skills/fullstack-engineer
```

### عام (متاح لكل مشاريعك)
```bash
git clone https://github.com/Wholorp/fullstack-engineer-skill.git \
  ~/.claude/skills/fullstack-engineer
```

تأكد إن `SKILL.md` في جذر المجلد:
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

السكل يفعّل تلقائياً لما تطلب:

- "ابني لي backend"
- "اعمل API لـ..."
- "ابغا مشروع كامل"
- "ابني نظام تسجيل دخول"
- "design a database schema"
- "create a fullstack app"
- "best practices for..."
- "scalable architecture"

أو فعّله صراحة:
```
/fullstack-engineer ابني لي نظام مهام
```

### مثال
```
انت: ابني لي API لإدارة المستخدمين
كلود: [يفعّل السكل تلقائياً]
       1. يسأل عن البيانات والصلاحيات
       2. يصمم schema (Prisma)
       3. يعرّف API contract (TypeScript types)
       4. يبني bottom-up: repo → service → controller → router
       5. يضيف Zod validation + error handling + tests
```

---

## ايش جوّاه؟

- **`SKILL.md`** — الفلسفة، الـstack الافتراضي، بنية المشروع، معايير الكود، الـworkflow
- **`references/backend-patterns.md`** — أنماط متقدمة: CQRS, queues, caching
- **`references/frontend-patterns.md`** — optimistic updates, infinite scroll, code splitting
- **`references/security-checklist.md`** — checklist كاملة قبل الـlaunch

---

## متى تستعمله؟

✅ مشروع جديد من الصفر
✅ refactor لمشروع قائم
✅ مراجعة كود (code review)
✅ تعليم best practices

❌ سكربت سريع لمرة واحدة (over-engineering)
❌ prototype للتجربة فقط

---

## الترخيص

MIT — استخدمه، عدّله، شاركه.

</div>
