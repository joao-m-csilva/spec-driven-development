<a id="en-us"></a>

# spec-driven-development

Language: **English (en-US)** | [Português (pt-BR)](#pt-br)

Personal toolkit for **Spec-Driven Development (SDD)** with AI coding agents.

The goal is simple: **write the spec first, then let agents implement it under strict, predictable rules.** This repository holds the reusable instructions, conventions, and templates used across all projects.

---

## What is Spec-Driven Development?

Instead of prompting an agent ad hoc ("build me a login page"), work starts from a written specification that becomes the source of truth for both humans and agents:

```text
Spec  ->  Plan  ->  Tasks  ->  Implement  ->  Test  ->  Commit & PR  ->  Review
```

| Step | Output |
|---|---|
| **Spec** | *What* and *why*: requirements, acceptance criteria, constraints |
| **Plan** | *How*: architecture, affected files, risks |
| **Tasks** | Small, verifiable units of work |
| **Implement** | Code that follows the project's `AGENTS.md` |
| **Test** | Automated tests proving each acceptance criterion |
| **Commit & PR** | One file per commit (Conventional Commits) and structured Pull Request |
| **Review** | Human validates the result against the spec |

---

## Contents

| File | Description |
|---|---|
| [`AGENTS.md`](AGENTS.md) | Global, language-agnostic rules for AI coding agents |
| `README.md` | This file (bilingual `en-US` and `pt-BR`) |

### `AGENTS.md` highlights

- **One commit per file & standardized Pull Requests** using [Conventional Commits](https://www.conventionalcommits.org/) and structured PR descriptions (`Summary`, `Key Changes`, `Verification`).
- **Every new function ships with a test** (Jest, Vitest, pytest, JUnit 5, xUnit, Go `testing`).
- **No unprotected APIs** — deny-by-default auth, per-resource authorization, rate limiting, input validation, CORS allow-lists, and security headers.
- **Code quality** — typed code, explicit error handling, no dead code, comments only when necessary (KISS) and always in English.
- **Documentation & README standards** — single-file bilingual `README.md` (`en-US` at `#en-us` and `pt-BR` at `#pt-br`), zero emojis, and zero AI marketing clichés.
- **Safe agent workflow** — Explore -> Plan -> Implement -> Test -> Commit -> Report, plus explicit permission boundaries.

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

```text
~/Developer/
├── AGENTS.md                  <- global rules (from this repo)
└── my-project/
    ├── AGENTS.md              <- stack, versions, install/dev/test/lint commands
    ├── backend/AGENTS.md      <- optional: API, database, migrations
    └── frontend/AGENTS.md     <- optional: components, UI testing, accessibility
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

> **Precedence:** explicit chat instructions -> nearest `AGENTS.md` -> global `AGENTS.md`.

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

---
---

<a id="pt-br"></a>

# spec-driven-development (PT-BR)

Idioma: [English (en-US) — Voltar ao topo](#en-us) | **Português (pt-BR)**

Kit de ferramentas pessoal para **Spec-Driven Development (SDD)** com agentes de programação baseados em IA.

O objetivo é direto: **escrever a especificação primeiro e deixar que os agentes a implementem sob regras estritas e previsíveis.** Este repositório armazena as instruções reutilizáveis, convenções e modelos aplicados em todos os projetos.

---

## O que é Spec-Driven Development?

Em vez de enviar prompts improvisados ao agente ("crie uma página de login"), o trabalho parte de uma especificação escrita que serve como fonte da verdade para humanos e agentes:

```text
Spec  ->  Plan  ->  Tasks  ->  Implement  ->  Test  ->  Commit & PR  ->  Review
```

| Etapa | Resultado |
|---|---|
| **Spec** | *O que* e *por que*: requisitos, critérios de aceite e restrições |
| **Plan** | *Como*: arquitetura, arquivos afetados e riscos |
| **Tasks** | Unidades pequenas e verificáveis de trabalho |
| **Implement** | Código que segue estritamente o `AGENTS.md` do projeto |
| **Test** | Testes automatizados comprovando cada critério de aceite |
| **Commit & PR** | Um arquivo por commit (Conventional Commits) e Pull Request estruturada |
| **Review** | Validação humana do resultado frente à especificação |

---

## Conteúdo

| Arquivo | Descrição |
|---|---|
| [`AGENTS.md`](AGENTS.md) | Regras globais e independentes de linguagem para agentes de IA |
| `README.md` | Este arquivo (bilíngue `en-US` e `pt-BR`) |

### Destaques do `AGENTS.md`

- **Um commit por arquivo e Pull Requests padronizadas** usando [Conventional Commits](https://www.conventionalcommits.org/) e descrições técnicas estruturadas (`Summary`, `Key Changes`, `Verification`).
- **Toda nova função acompanha teste automatizado** (Jest, Vitest, pytest, JUnit 5, xUnit, Go `testing`).
- **Nenhuma API desprotegida** — autenticação obrigatória por padrão, autorização por recurso, rate limiting, validação de entrada com schema, lista explícita de origens no CORS e cabeçalhos de segurança.
- **Qualidade de código** — tipagem estrita, tratamento explícito de erros, ausência de código morto, comentários apenas quando necessários (KISS) e sempre em inglês.
- **Padrões de documentação e README** — arquivo único `README.md` bilíngue (`en-US` em `#en-us` e `pt-BR` em `#pt-br`), sem emojis e sem clichês de marketing gerados por IA.
- **Fluxo seguro de trabalho** — Explore -> Plan -> Implement -> Test -> Commit -> Report, além de limites explícitos para ações que exigem permissão.

---

## Como Usar

### 1. Regras globais para todos os projetos

Posicione o `AGENTS.md` na raiz do seu diretório de trabalho para que todos os projetos abaixo dele herdem as diretrizes:

```bash
# Um symlink mantém todos os projetos sincronizados com este repositório
ln -s ~/Developer/spec-driven-development/AGENTS.md ~/Developer/AGENTS.md
```

### 2. Sobrescritas específicas por projeto

Os agentes utilizam o `AGENTS.md` **mais próximo do arquivo que está sendo editado**. Adicione um arquivo menor por projeto (ou por camada em um monorepo) contendo apenas o que for específico:

```text
~/Developer/
├── AGENTS.md                  <- regras globais (deste repositório)
└── my-project/
    ├── AGENTS.md              <- stack, versões e comandos install/dev/test/lint
    ├── backend/AGENTS.md      <- opcional: API, banco de dados e migrations
    └── frontend/AGENTS.md     <- opcional: componentes, testes de UI e acessibilidade
```

Exemplo de arquivo de projeto:

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

> **Precedência:** instruções explícitas no chat -> `AGENTS.md` mais próximo -> `AGENTS.md` global.

---

## Compatibilidade

`AGENTS.md` é uma convenção aberta lida pela maioria das ferramentas de programação com IA, incluindo Antigravity, OpenAI Codex, Cursor, GitHub Copilot, Jules, entre outras.

---

## Roadmap

- [ ] Modelo de especificação (`specs/_template.md`) com requisitos e critérios de aceite
- [ ] Modelos de plano e tarefas
- [ ] Modelos de `AGENTS.md` por camada (backend, frontend, monorepo)
- [ ] Skills e fluxos de trabalho reutilizáveis para agentes

---

## Contribuição

Este é um kit de ferramentas pessoal, mas sugestões são bem-vindas via issues ou pull requests. Todas as alterações seguem as regras definidas em [`AGENTS.md`](AGENTS.md).

[Voltar ao topo (EN-US)](#en-us)
