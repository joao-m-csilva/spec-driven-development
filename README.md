# spec-driven-development

My personal toolkit for **Spec-Driven Development (SDD)** with AI coding agents.

The goal is simple: **write the spec first, then let agents implement it under strict, predictable rules.** This repository holds the reusable instructions, conventions, and templates I use across all my projects.

---

## What is Spec-Driven Development?

Instead of prompting an agent ad hoc ("build me a login page"), work starts from a written specification that becomes the source of truth for both humans and agents:

```
Spec  →  Plan  →  Tasks  →  Implement  →  Test  →  Commit  →  Review
```

| Step          | Output                                                           |
|---------------|------------------------------------------------------------------|
| **Spec**      | *What* and *why*: requirements, acceptance criteria, constraints |
| **Plan**      | *How*: architecture, affected files, risks                       |
| **Tasks**     | Small, verifiable units of work                                  |
| **Implement** | Code that follows the project's `AGENTS.md`                      |
| **Test**      | Automated tests proving each acceptance criterion                |
| **Commit**    | One file per commit, Conventional Commits                        |
| **Review**    | Human validates the result against the spec                      |

---

## Contents

| File                     | Description                                                       |
|--------------------------|-------------------------------------------------------------------|
| [`AGENTS.md`](AGENTS.md) | Global, language-agnostic rules for AI coding agents              |
| `README.md`              | This file                                                         |

### `AGENTS.md` highlights

- **One commit per file** using [Conventional Commits](https://www.conventionalcommits.org/).
- **Every new function ships with a test** (Jest, pytest, JUnit 5, xUnit, Go `testing`…).
- **No unprotected APIs** — deny-by-default auth, per-resource authorization, rate limiting, input validation, CORS allow-lists, and security headers.
- **Code quality** — typed code, explicit error handling, no dead code, comments only when necessary (KISS) and always in English.
- **Safe agent workflow** — Explore → Plan → Implement → Test → Commit → Report, plus a list of actions that require explicit permission.

---

## Usage

### 1. Global rules for all projects

Place `AGENTS.md` at the root of your workspace so every project below it inherits the rules:

```bash
# Symlink keeps every project in sync with this repo
ln -s ~/Developer/spec-driven-development/AGENTS.md ~/Developer/AGENTS.md
```

### 2. Project-specific overrides

Agents use the `AGENTS.md` **closest to the file being edited**. Add a smaller file per project (or per layer in a monorepo) containing only what differs:

```
~/Developer/
├── AGENTS.md                  ← global rules (from this repo)
└── my-project/
    ├── AGENTS.md              ← stack, versions, install/dev/test/lint commands
    ├── backend/AGENTS.md      ← optional: API, database, migrations
    └── frontend/AGENTS.md     ← optional: components, UI testing, accessibility
```

Example project file:

```markdown
# AGENTS.md — my-project
## Stack
- Node 20, Next.js 15, TypeScript (strict)
## Commands
- Install: `npm install`
- Dev: `npm run dev`
- Test: `npm test`
- Lint: `npm run lint`
```

> **Precedence:** explicit chat instructions → nearest `AGENTS.md` → global `AGENTS.md`.

---

## Compatibility

`AGENTS.md` is an open convention read by most AI coding tools, including Antigravity, OpenAI Codex, Cursor, GitHub Copilot, Jules, and others.

---

## Roadmap

- [ ] Spec template (`specs/_template.md`) with requirements and acceptance criteria
- [ ] Plan and task templates
- [ ] Project-level `AGENTS.md` templates (backend, frontend, monorepo)
- [ ] Reusable agent skills and workflows

---

## Contributing

This is a personal toolkit, but suggestions are welcome via issues or pull requests. All changes follow the rules in [`AGENTS.md`](AGENTS.md).
