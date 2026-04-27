# Security Checklist — Pre-Launch

## Authentication & Authorization
- [ ] Passwords hashed with bcrypt (min 12 rounds) or argon2
- [ ] JWT secrets are at least 256-bit random strings, stored in env
- [ ] Refresh tokens stored in httpOnly cookies, not localStorage
- [ ] Access tokens short-lived (15min), refresh tokens rotatable
- [ ] Logout invalidates refresh token in DB
- [ ] Route-level auth middleware applied (no endpoints accidentally public)
- [ ] Role checks done server-side, never trust client claims

## Input Validation
- [ ] All request bodies validated with Zod / Pydantic at controller entry
- [ ] File uploads: validate MIME type server-side, not just extension
- [ ] Path parameters validated and sanitized
- [ ] Query parameters have max length limits

## API Security
- [ ] Rate limiting on auth endpoints (login, register, forgot-password)
- [ ] Helmet.js (or equivalent) sets security headers
- [ ] CORS configured to specific origins — not `*` in production
- [ ] SQL injection impossible (using ORM/parameterized queries)
- [ ] No sensitive data in URL query strings (use body/headers)

## Data & Privacy
- [ ] PII fields encrypted at rest if required by compliance
- [ ] Logs don't contain passwords, tokens, or full credit card numbers
- [ ] Soft delete for user data (support account deletion requests)
- [ ] DB backups automated and tested

## Infrastructure
- [ ] `.env` is in `.gitignore` — secrets never committed
- [ ] Docker image uses non-root user
- [ ] Dependencies audited (`npm audit` / `pip-audit`) before launch
- [ ] HTTPS enforced — HTTP redirects to HTTPS
- [ ] Error messages in production don't reveal stack traces or DB structure
