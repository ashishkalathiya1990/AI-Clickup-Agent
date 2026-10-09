# CLAUDE.md

> Read by the ClickUp AI agent (Claude Code) before every analysis and implementation.
> This repo is NEW and currently empty. Update this file as soon as the stack is decided.

## Project status

- **Stage:** New, empty repository. No application code or tech stack chosen yet.
- **Product:** <Product name> — <one sentence on what it will do and who will use it>
- **Owner:** Comfort Click development team

## Rules while the repo is empty

1. **Do not pick a stack silently.** If a ClickUp task needs code and no stack is recorded under "Tech stack" below, the analysis comment must propose 1–3 stack options with trade-offs and a recommendation, then wait for a human to choose.
2. **The first implementation task scaffolds the project.** After the stack is agreed in the task comments, the PR must include:
    - the project skeleton created with the framework's official generator (no hand-rolled scaffolding),
    - a `README.md` with setup, run, lint and test commands,
    - a lint/format setup and one passing example test,
    - a `.gitignore` and a `.env.example` (never a real `.env`),
    - an update to THIS file: fill in "Tech stack", "Folder layout" and "Commands", and delete this "Rules while the repo is empty" section.
3. **Keep the first version small.** Build only what the task asks for; no extra pages, features or dependencies "for later".
4. Prefer mainstream, well-documented, long-term-supported versions of any framework.

## Tech stack

_Not decided yet. Fill in after the first scaffolding PR is merged._

- Language / framework: <…>
- Frontend: <…>
- Database: <…>
- Package manager: <…>
- Hosting / deployment: <…>

## Folder layout

_Not decided yet. Describe where pages, API code, business logic and tests live._

## Commands

_Not decided yet._

```bash
<install>
<lint>     # the agent runs ONLY this before opening a PR
<test>     # runs in PR CI, not in the agent
<dev>
```

## Coding conventions

- Follow the framework's official conventions and style guide.
- Keep changes focused on the task; no unrelated refactors.
- Add or update tests for any behaviour change.
- User-facing text in UK English.
- Never hard-code secrets, API keys or URLs that differ per environment; read them from environment variables and document them in `.env.example`.

## Do not touch

- `.env*` files with real values, secrets, credentials or keys — never read, print or commit them.
- `.github/workflows/` and `scripts/clickup-agent/` — the agent must not modify its own setup.
- Anything under `vendor/`, `node_modules/` or build output folders.

## Git and pull requests

- Base branch: `main`. Never push to it directly, never force-push, never merge.
- Branch names: `ai-agent/<task_id>-<short-slug>`.
- Commits: Conventional Commits (`feat(scope): …`, `fix(scope): …`, `chore: …`).
- PR description: what changed, why, how to test it manually, and any risks or open questions.

## ClickUp comments

- Readers are product, QA and business stakeholders — write in plain English.
- No file paths, class names or code in ClickUp comments; technical detail goes in the PR.
- If requirements are unclear, ask numbered questions instead of guessing.
