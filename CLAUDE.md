# CLAUDE.md

> Read by the ClickUp AI agent (Claude Code) before every analysis and implementation.
> Keep this file accurate: update it in the same PR whenever the stack, structure or commands change.

## Project overview

- **Product:** AI-Clickup-Agent — a Laravel web application (starting with user accounts).
- **Repo:** `ashishkalathiya1990/AI-Clickup-Agent`
- **Stage:** New project. The first task scaffolds Laravel with authentication.
- **First milestone:** user registration, login, logout and password reset, on MySQL, with a simple UI.

## Tech stack

- **Backend:** PHP 8.3+, latest stable Laravel (12.x or newer)
- **Database:** MySQL 8 (local and production). Tests use SQLite in-memory (Laravel default `phpunit.xml`).
- **Auth:** the official Laravel **Livewire starter kit** (Blade + Livewire, Tailwind CSS). It provides registration, login, logout, password reset and email verification out of the box.
- **Frontend build:** Vite + Tailwind CSS (as shipped by the starter kit)
- **Package managers:** Composer (PHP), npm (JS/CSS)
- **Code style:** Laravel Pint (`./vendor/bin/pint`)
- **Tests:** Pest or PHPUnit, whichever the starter kit ships

Do not add other UI frameworks (React, Vue, Inertia, Bootstrap) or auth packages (Jetstream, Fortify customisation, Sanctum, Socialite) unless a task asks for them.

## First task: scaffolding the project

The repository already contains `CLAUDE.md`, `.gitignore`, `.github/` and `scripts/clickup-agent/`. Those must be kept.

1. Create the app in a temporary folder, then move it into the repo root without overwriting the existing files:
   ```bash
   composer create-project laravel/livewire-starter-kit /tmp/app
   rsync -a --ignore-existing /tmp/app/ ./
   ```
   Then merge Laravel's `.gitignore` entries into the existing `.gitignore`. Keep `.agent-state/` and `.agent-out/`.
2. Configure `.env.example` for MySQL:
   ```
   APP_NAME="AI Clickup Agent"
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=ai_clickup_agent
   DB_USERNAME=root
   DB_PASSWORD=
   MAIL_MAILER=log
   ```
   `MAIL_MAILER=log` makes password-reset emails go to `storage/logs/laravel.log` in local development.
3. Keep the starter kit's auth screens: register, login, logout, forgot password, reset password, a simple dashboard after login, and profile settings.
4. Keep the UI simple: starter-kit defaults, no custom theme, no extra pages.
5. Run `./vendor/bin/pint` and make sure the starter kit's auth tests still pass with `php artisan test`.
6. Write a `README.md` with local setup steps (see Commands).
7. Update this file: replace "Stage: New project" with the real state, and delete this "First task" section.

## Folder layout (Laravel standard)

```
app/Http/Controllers/   HTTP controllers (thin — no business logic)
app/Livewire/           Livewire components (auth + settings from the starter kit)
app/Models/             Eloquent models (User, …)
app/Services/           Business logic for new features
database/migrations/    Schema changes — always add a new migration, never edit a merged one
database/factories/     Model factories for tests
resources/views/        Blade views (layouts, components, livewire)
routes/web.php          Web routes
routes/auth.php         Auth routes from the starter kit
tests/Feature/          Feature tests (auth flows live here)
tests/Unit/             Unit tests
```

## Commands

```bash
composer install
npm install
cp .env.example .env && php artisan key:generate
php artisan migrate               # needs MySQL running and the database created
npm run build                     # or: npm run dev
php artisan serve                 # http://127.0.0.1:8000
composer run dev                  # serve + vite + queue together (starter kit script)

./vendor/bin/pint --test          # code style check — the agent's quick QA before a PR
php artisan test                  # full test suite — runs in PR CI
```

## Coding conventions

- Follow Laravel conventions and match the existing code around your change.
- Controllers stay thin: validate with Form Requests, put logic in `app/Services/` or model methods.
- Use Eloquent and migrations. No raw SQL unless there is a clear performance reason, noted in the PR.
- Every new route that needs a logged-in user goes in the `auth` middleware group. Add `verified` if the user must have verified their email.
- Passwords always use Laravel's hashing (`Hash::make` or the `hashed` cast). Never log or display them.
- Add or update Feature tests for any behaviour change.
- User-facing text in UK English, short and plain.
- Run `./vendor/bin/pint` before committing.

## Do not touch

- `.env` (real values), secrets, API keys or credentials. Never read, print or commit them; document new variables in `.env.example`.
- Migrations already merged to `main`. Add a new migration instead.
- `vendor/`, `node_modules/`, `public/build/`, `storage/` contents.
- `.github/workflows/` and `scripts/clickup-agent/`. The agent must not modify its own setup.
- Starter-kit auth logic (password hashing, rate limiting, CSRF, session handling). Propose security changes in the analysis comment; implement them only with explicit approval in the task.

## Git and pull requests

- Base branch: `main`. Never push to it directly, never force-push, never merge.
- Branch names: `ai-agent/<task_id>-<short-slug>`.
- Commits: Conventional Commits (`feat(auth): …`, `fix(profile): …`, `chore: …`).
- PR description: what changed, why, how to test it locally (steps + URLs), and any risks or open questions.

## ClickUp comments

- Readers are product, QA and business stakeholders. Write in plain English.
- No file paths, class names or code in ClickUp comments; technical detail goes in the PR.
- If requirements are unclear, ask numbered questions instead of guessing.
