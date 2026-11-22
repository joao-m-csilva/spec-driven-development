# AGENTS.md

Global instructions for AI coding agents working in any project under this directory.

> **Precedence:** The `AGENTS.md` closest to the file being edited wins. Sub-projects
> (e.g. `rubberduck-dev/AGENTS.md`, `rubberduck-dev/backend/AGENTS.md`) may add or
> override rules defined here. Explicit user instructions in chat override everything.

---

## 1. Core Principles

- **Understand before changing.** Read the relevant code, README, and existing tests before editing.
- **Follow existing conventions.** Match the project's style, structure, naming, and libraries. Do not introduce a new framework, dependency, or pattern without asking.
- **Small, focused changes.** Do only what was requested. No unrelated refactors or drive-by fixes; mention them in the report instead.
- **Never break the build.** Every commit must compile, lint, and pass the test suite.
- **Ask when unsure.** If requirements are ambiguous or a change is destructive, stop and ask.
- **Preserve documentation.** Keep existing comments and docstrings unless your change makes them incorrect.

---

## 2. Commits & Pull Requests

### Commit Rules
1. **One commit per logical change.** A logical change is the smallest set of files that builds and passes tests together (for example, a function and its test, or a rename and all its call sites). Do not bundle unrelated changes, and do not split a change in a way that leaves any commit failing.
2. Stage files explicitly (`git add <path>`); never `git add .` or `git add -A`.
3. Use **[Conventional Commits](https://www.conventionalcommits.org/)**:
   ```
   <type>(<scope>): <short imperative summary, max 72 chars>

   [optional body: what and WHY, not how]
   ```
   Types: `feat`, `fix`, `test`, `refactor`, `docs`, `style`, `perf`, `chore`, `ci`, `security`.
4. **Order within a change:** implementation commit first, then its test commit, only if the implementation commit still passes the existing suite on its own. Otherwise, commit them together and say so in the body.
5. Dependency manifests and their lockfiles are always committed together, as are generated files and their sources.
6. Never commit: secrets, `.env` files, credentials, build artifacts, `node_modules`, `__pycache__`, IDE settings, or large binaries.
7. Never `git push --force` to shared branches or rewrite published history. Push the feature branch to `origin` once all verification checks pass.
8. Work on a feature branch (`feat/<short-name>`, `fix/<short-name>`). Never commit directly to `main`/`master` unless instructed.

### Pull Request Rules
1. **When to open:** automatically after all commits for the task are complete and tests, linters, and build checks pass. Requires `gh` installed and authenticated; if it is not, report that instead of working around it.
2. **Command:** `gh pr create --base main --head <branch>`.
3. **Title:** Conventional Commits format, in English.
4. **Description:** in English, strictly technical (no emojis, no marketing language), with three sections:
   - `## Summary` — what was implemented or fixed, and why.
   - `## Key Changes` — bulleted breakdown grouped by module.
   - `## Verification` — exact test, typecheck, lint, and build commands run, with results.

---

## 3. Testing

**Every new function, method, or endpoint ships with at least one automated test.** Modified functions must have their tests updated.

Use the framework already in the project. If none exists, default to:

| Language              | Framework                          | Test file convention                  |
|-----------------------|------------------------------------|---------------------------------------|
| JavaScript/TypeScript | Jest (Vitest if Vite-based)        | `*.test.ts` / `*.spec.ts`             |
| React components      | Jest + React Testing Library       | `Component.test.tsx`                  |
| Python                | pytest                             | `tests/test_<module>.py`              |
| Java                  | JUnit 5 + Mockito                  | `src/test/java/.../<Class>Test.java`  |
| C# / .NET             | xUnit (+ Moq / NSubstitute)        | `<Project>.Tests/<Class>Tests.cs`     |
| Go                    | `testing` package                  | `<file>_test.go`                      |

### Coverage of each test set
- Happy path, edge cases (empty, null, boundaries), and error cases.
- For API endpoints: 400/422 (invalid payload), 401 (unauthenticated), 403 (unauthorized), 429 (rate-limited).

### Quality rules
- Arrange, Act, Assert. One behavior per test, with a descriptive name (`should return 401 when token is missing`).
- Deterministic and isolated: mock network, time, filesystem, and external services.
- Never delete, skip (`.skip`, `@Disabled`, `@pytest.mark.skip`), or weaken a failing test to make it pass. Fix the code, or ask.
- Do not lower coverage thresholds.
- **If tests already fail before your change,** do not fix them as a side effect. Record which tests fail, proceed with your task, and report the pre-existing failures.

---

## 4. API Security (applies only to code that exposes an HTTP API)

Every endpoint must be protected. Public endpoints must be explicitly justified and still rate-limited. Use the project's existing libraries for each item below.

- **Authentication:** deny by default; mark public routes explicitly. Validate token signature, `exp`, `iss`, and `aud` on every request. On the web, keep tokens in `HttpOnly`, `Secure`, `SameSite` cookies, never in `localStorage`. Hash passwords with bcrypt or argon2.
- **Authorization:** check permissions per resource, not just "is logged in" (prevent IDOR). Apply least privilege.
- **Rate limiting:** apply to all endpoints, stricter on auth routes (login, signup, password reset, OTP). Return `429` with `Retry-After`. Cap request body size and page size, and set request timeouts.
- **Input and output:** validate all input with a schema and reject unknown fields. Use parameterized queries or an ORM. Never return stack traces, internal errors, or sensitive fields. Use correct status codes (`400`, `401`, `403`, `404`, `422`, `429`, `500`).
- **Transport:** HTTPS only in production. CORS with an explicit origin allow-list (never `*` with credentials). Set `Strict-Transport-Security`, `X-Content-Type-Options`, `X-Frame-Options`, and `Content-Security-Policy`.
- **Secrets:** from environment variables or a secrets manager, never hardcoded. Keep a committed `.env.example` with placeholders; `.env` stays in `.gitignore`. If you find a leaked secret, stop and alert the user.

---

## 5. Code Quality

- **Readable over clever.** Descriptive names and small, single-purpose functions.
- **Type everything** where the language supports it (TypeScript `strict`, Python type hints, no `any` without justification).
- **Handle errors explicitly.** No empty `catch` blocks or swallowed exceptions.
- **No dead code**, commented-out code, or leftover debug output (`console.log`, `print`).
- **Comments explain why, not what.** Use them for business rules, workarounds, and non-obvious decisions. Internal code needs no docstrings.
- **Public API docs.** Exported or public functions get JSDoc/docstring/XML docs covering purpose, params, return, and errors thrown.
- **Language:** comments, docstrings, commit messages, and identifiers are in English. The only exception is the pt-BR section of `README.md` (Section 6).
- Run the project's linter and formatter before committing.

---

## 6. Documentation & README Standards

Project documentation is sober, technical engineering reference material.

### 6.1 Language Policy (single file, en-US on top, pt-BR below)
- Use a single `README.md`. Do not create separate language files such as `README.pt-BR.md`.
- **Top section (en-US):** starts with `<a id="en-us"></a>`, the project title, the line `Language: **English (en-US)** | [Português (pt-BR)](#pt-br)`, then the full documentation in English.
- **Bottom section (pt-BR):** after a horizontal rule (`---`), starts with `<a id="pt-br"></a>`, the project title, the line `Idioma: [English (en-US)](#en-us) | **Português (pt-BR)**`, then the complete, synchronized documentation in Brazilian Portuguese.
- When you change one section, update the other in the same commit.

### 6.2 Style
- No emojis in `README.md`, `AGENTS.md`, or technical docs.
- No promotional language ("blazingly fast", "seamless", "cutting-edge", "robust", "modern", "effortless"). State facts, constraints, and behavior directly.
- Prefer tables, exact file paths, and copy-pasteable commands over narrative.

### 6.3 Required README structure (both languages, in order)
1. **Title, language switcher, overview:** what the system does and the problem it solves.
2. **Tech stack and architecture:** runtime, frameworks, libraries, directory structure with module responsibilities.
3. **Prerequisites and environment variables:** required tool versions, and a table covering every variable in `.env.example` (Variable, Required, Default, Description).
4. **Setup, development, and verification commands:** install, run locally, test, typecheck, lint, production build.
5. **Database and API specification (if applicable):** migrations, schema overview, endpoints, authentication, rate limits, status codes.
6. **Deployment:** concrete production deploy and configuration steps.

---

## 7. Dependencies

- Prefer the standard library and existing dependencies.
- Before adding a package, confirm it is maintained, widely used, and license-compatible, and ask the user.
- Keep versions pinned through the lockfile. Run `npm audit`, `pip-audit`, or the equivalent after adding dependencies.

---

## 8. Agent Workflow

1. **Explore:** read related files, tests, and the project-level `AGENTS.md` and README.
2. **Plan:** for non-trivial tasks, outline steps and affected files before coding.
3. **Implement:** follow the rules above.
4. **Verify:** write or update tests, then run the full suite, linter, and type checker.
5. **Commit & PR:** one logical change per commit using Conventional Commits, then push the feature branch and open a Pull Request.
6. **Report:** summarize what changed, what was tested, pre-existing failures, and open questions.

### Finding verification commands
Use the commands documented in the project's `AGENTS.md` or README. If none are documented, read the build manifest (`package.json` scripts, `Makefile`, `pyproject.toml`, `pom.xml`, `*.csproj`) and propose the commands to the user before running anything that is not a plain test, lint, or build command.

### Never do without explicit permission
- Delete files or directories outside the task scope.
- Run destructive commands (`rm -rf`, `DROP TABLE`, `git reset --hard`, `git push --force`).
- Modify CI/CD pipelines, infrastructure, or production configuration.
- Change database schemas without a migration file.
- Disable security checks, linters, or tests.

---

## 9. Project-Specific Overrides

Each project should provide its own `AGENTS.md` with:
- Stack and versions (e.g. Node 20, Next.js 15, Python 3.12, Java 21, .NET 8)
- Install, build, test, and lint commands
- Folder structure and architecture notes
- Conventions that differ from this file

Example:
```markdown
# AGENTS.md — rubberduck-dev
## Commands
- Install: `npm install`
- Dev: `npm run dev`
- Test: `npm test`
- Lint: `npm run lint`
```
