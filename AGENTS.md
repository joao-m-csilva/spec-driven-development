# AGENTS.md

Global instructions for AI coding agents working in any project under this directory.

> **Precedence:** The `AGENTS.md` closest to the file being edited wins. Sub-projects
> (e.g. `rubberduck-dev/AGENTS.md`, `rubberduck-dev/backend/AGENTS.md`) may add or
> override rules defined here. Explicit user instructions in chat override everything.

---

## 1. Core Principles

- **Understand before changing.** Read the relevant code, README, and existing tests before editing.
- **Follow existing conventions.** Match the project's style, structure, naming, and libraries. Do not introduce a new framework, dependency, or pattern without asking.
- **Small, focused changes.** Do only what was requested. No unrelated refactors or "drive-by" fixes — mention them instead.
- **Never break the build.** Code must compile, lint, and pass all tests before every commit.
- **Ask when unsure.** If requirements are ambiguous or a change is destructive, stop and ask.
- **Preserve documentation.** Keep existing comments and docstrings unless they become incorrect due to your change.

---

## 2. Commits — One Commit per File

**Every changed file gets its own commit.** Never bundle multiple files into one commit.

### Rules
1. Stage and commit files individually:
   ```bash
   git add path/to/file.ts
   git commit -m "feat(auth): add token validation helper"
   ```
2. Use **[Conventional Commits](https://www.conventionalcommits.org/)**:
   ```
   <type>(<scope>): <short imperative summary, max 72 chars>

   [optional body: what and WHY, not how]
   ```
   | Type       | Use for                                   |
   |------------|-------------------------------------------|
   | `feat`     | New feature / function                    |
   | `fix`      | Bug fix                                   |
   | `test`     | Adding or updating tests                  |
   | `refactor` | Code change with no behavior change       |
   | `docs`     | Documentation only                        |
   | `style`    | Formatting, no logic change               |
   | `perf`     | Performance improvement                   |
   | `chore`    | Tooling, config, dependencies             |
   | `ci`       | CI/CD pipeline changes                    |
   | `security` | Security hardening                        |
3. **Commit order:** implementation file first, then its test file, so history stays readable (`feat: ...` → `test: ...`).
4. Each commit message must describe the change *in that file* specifically.
5. **Allowed exceptions** (files that are invalid if split): dependency manifest + lockfile (`package.json` + `package-lock.json`, `pyproject.toml` + `poetry.lock`, etc.), and generated files that must stay in sync with their source. Explain the exception in the commit body.
6. Never commit: secrets, `.env` files, credentials, build artifacts, `node_modules`, `__pycache__`, IDE settings, or large binaries.
7. Never `git push --force` to shared branches, rewrite published history, or push without being asked.
8. Work on a feature branch (`feat/<short-name>`, `fix/<short-name>`) — never commit directly to `main`/`master` unless instructed.

---

## 3. Testing — Every New Function Has a Test

**Every new function, method, or endpoint must ship with at least one automated test.** Modified functions must have their tests updated.

### Framework by language
Use the framework already in the project. If none exists, default to:

| Language              | Framework                          | Test file convention                  |
|-----------------------|------------------------------------|---------------------------------------|
| JavaScript/TypeScript | **Jest** (or Vitest if Vite-based) | `*.test.ts` / `*.spec.ts` next to source or in `__tests__/` |
| React components      | Jest + React Testing Library       | `Component.test.tsx`                  |
| Python                | **pytest**                         | `tests/test_<module>.py`              |
| Java                  | **JUnit 5** + Mockito              | `src/test/java/.../<Class>Test.java`  |
| C# / .NET             | **xUnit** (+ Moq / NSubstitute)    | `<Project>.Tests/<Class>Tests.cs`     |
| Go                    | `testing` package                  | `<file>_test.go`                      |

### What a test must cover
- ✅ **Happy path** — expected input produces expected output.
- ✅ **Edge cases** — empty, null/undefined, boundaries, large inputs.
- ✅ **Error cases** — invalid input throws/returns the correct error.
- ✅ **Security cases for APIs** — unauthenticated (401), unauthorized (403), rate-limited (429), invalid payload (400/422).

### Test quality rules
- Follow **Arrange → Act → Assert**.
- One behavior per test; descriptive names: `should return 401 when token is missing`.
- Tests must be **deterministic and isolated** — mock network, time, filesystem, and external services.
- Never delete, skip (`.skip`, `@Disabled`, `@pytest.mark.skip`), or weaken a failing test to make it pass. Fix the code, or ask.
- Run the full test suite before committing. Do not lower coverage thresholds.

---

## 4. API Security — No Unprotected Endpoints

**Every API endpoint must be protected against unauthorized and abusive requests.** Public endpoints must be explicitly justified and still rate-limited.

### 4.1 Authentication
- Require authentication on all endpoints by default (deny-by-default); mark public routes explicitly.
- Use standard headers: `Authorization: Bearer <token>` (JWT/OAuth2) or `X-API-Key` for service-to-service.
- Validate tokens on every request: signature, expiration (`exp`), issuer (`iss`), audience (`aud`).
- Use short-lived access tokens + refresh tokens. Store tokens in `HttpOnly`, `Secure`, `SameSite` cookies on the web — never in `localStorage`.
- Hash passwords with **bcrypt / argon2** — never plain text or fast hashes (MD5/SHA1).

### 4.2 Authorization
- Check **permissions per resource**, not just "is logged in" (prevent IDOR: user A must not access user B's data).
- Apply least privilege / role-based access control.

### 4.3 Rate Limiting & Abuse Protection
- Apply rate limits to all endpoints; stricter limits on auth endpoints (login, signup, password reset, OTP).
  - Node: `express-rate-limit` / `@nestjs/throttler`
  - Python: `slowapi` (FastAPI) / `django-ratelimit` / `Flask-Limiter`
  - Java: Bucket4j / Resilience4j
  - .NET: built-in `Microsoft.AspNetCore.RateLimiting`
- Return `429 Too Many Requests` with a `Retry-After` header.
- Limit request body size and pagination size (`limit` max, e.g. 100).
- Set request timeouts.

### 4.4 Input & Output
- **Validate all input** with a schema (Zod, Joi, Pydantic, Bean Validation, FluentValidation). Reject unknown fields.
- Use parameterized queries / ORM — never string-concatenated SQL.
- Never return stack traces, internal errors, or sensitive fields (passwords, tokens, internal IDs) in responses.
- Use correct status codes: `400`, `401`, `403`, `404`, `422`, `429`, `500`.

### 4.5 Transport & Headers
- HTTPS only in production.
- Configure **CORS** with an explicit allow-list of origins — never `*` with credentials.
- Set security headers (Helmet in Node, or equivalent): `Strict-Transport-Security`, `X-Content-Type-Options`, `X-Frame-Options`, `Content-Security-Policy`.

### 4.6 Secrets
- Secrets come from environment variables or a secrets manager — **never hardcoded**.
- Keep a committed `.env.example` with placeholder values; `.env` stays in `.gitignore`.
- If you spot a leaked secret, stop and alert the user.

---

## 5. Code Quality

- **Readable over clever.** Descriptive names, small single-purpose functions (aim < 40 lines).
- **Type everything** where the language supports it (TypeScript `strict`, Python type hints, no `any` without justification).
- **Handle errors explicitly.** No empty `catch` blocks; no swallowed exceptions.
- **No dead code**, commented-out code, or leftover `console.log` / `print` debugging.
- **DRY, but not prematurely.** Extract duplication after it appears the third time.
- **Comments only when necessary (KISS).** Prefer self-explanatory code (clear names, small functions) over comments. Comment the *why* (business rules, workarounds, non-obvious decisions) — never restate *what* the code does.
- **All comments, docstrings, commit messages, and identifiers must be in English.**
- Document public functions with JSDoc / docstrings / XML docs: purpose, params, return, errors thrown.
- Run the project's linter and formatter (ESLint/Prettier, Ruff/Black, Checkstyle, `dotnet format`) before committing.

---

## 6. Dependencies

- Prefer the standard library and existing dependencies.
- Before adding a package: confirm it is maintained, widely used, and license-compatible — and ask the user.
- Pin versions via lockfiles. Run `npm audit` / `pip-audit` / equivalent after adding dependencies.

---

## 7. Agent Workflow

1. **Explore** — read related files, tests, and project-level `AGENTS.md` / README.
2. **Plan** — for non-trivial tasks, outline the steps and affected files before coding.
3. **Implement** — make the change following the rules above.
4. **Test** — write/update tests; run the full suite, linter, and type checker.
5. **Commit** — one file per commit, Conventional Commit messages.
6. **Report** — summarize what changed, what was tested, and any open questions or follow-ups.

### Never do without explicit permission
- Delete files or directories outside the task scope.
- Run destructive commands (`rm -rf`, `DROP TABLE`, `git reset --hard`, `git push --force`).
- Modify CI/CD pipelines, infrastructure, or production configuration.
- Change database schemas without a migration file.
- Disable security checks, linters, or tests.

---

## 8. Project-Specific Overrides

Each project should provide its own `AGENTS.md` with:
- **Stack & versions** (e.g. Node 20, Next.js 15, Python 3.12, Java 21, .NET 8)
- **Setup / build / test / lint commands**
- **Folder structure** and architecture notes
- **Project-specific conventions** that differ from this file

Example:
```markdown
# AGENTS.md — rubberduck-dev
## Commands
- Install: `npm install`
- Dev: `npm run dev`
- Test: `npm test`
- Lint: `npm run lint`
```
