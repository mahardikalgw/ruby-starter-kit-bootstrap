# Day-zero checklist

Ordered. Each item has an acceptance criterion, because "done" without one is an opinion.

## Phase 1 — the repository (30 min)

- [ ] `.ruby-version` pinned; `Gemfile` declares the Rails version.
      **Accept:** `ruby -v` matches the file.
- [ ] Node version pinned (`.nvmrc` or `engines` in `package.json`).
      **Accept:** `node -v` matches in CI and locally.
- [ ] `bin/setup` creates, migrates and seeds without a manual step, and is **idempotent**.
      **Accept:** run it twice; the second run changes nothing.
- [ ] `.env.example` lists every variable with a comment saying what breaks without it.
      **Accept:** a fresh clone boots with the example copied and nothing filled in.
- [ ] A `.gitignore` that covers `.env*`, `node_modules`, `tmp`, `storage`, `public/vite*`.
      **Accept:** `git status` after `bin/dev` is clean.

## Phase 2 — the database (20 min)

- [ ] PostgreSQL 16 in `config/database.yml` for development, test and production; no SQLite anywhere.
      **Accept:** `grep -r sqlite3 Gemfile` returns nothing.
- [ ] `PGHOST`/`PGPORT`/`PGUSER`/`PGPASSWORD` overridable, defaults that work on macOS Homebrew.
      **Accept:** a fresh machine with only Postgres installed can run `db:prepare`.
- [ ] Extensions the product needs, enabled by a migration (`pgcrypto`, `pg_trgm`, `vector`).
      **Accept:** `SELECT extname FROM pg_extension` lists them.
- [ ] The schema dump is committed and matches the migrations.
      **Accept:** `db:migrate` then `git diff db/` is empty.

## Phase 3 — the frontend (30 min)

- [ ] Vite + Inertia entrypoint (`application.tsx`) and a stylesheet with `@import 'tailwindcss'`.
      **Accept:** `bin/dev` serves a page through Inertia (the response contains `data-page`).
- [ ] `components.json` for the shadcn registry; one component installed as a smoke test.
      **Accept:** `npx shadcn@latest add button` writes into `components/ui`.
- [ ] `types.ts` with the shared prop interfaces (`auth`, `flash`, `errors`, `labels`, `nav`).
      **Accept:** `npm run check` passes on an empty page.
- [ ] Four layouts created: `app-layout`, `dashboard-layout`, `auth-layout`, `marketing-layout`.
      **Accept:** a page importing each one renders without a warning.

## Phase 4 — gates (45 min)

- [ ] `bin/rubocop` (Omakase) green.
      **Accept:** exits 0 on a fresh clone.
- [ ] `bin/bundler-audit` and `npm audit --audit-level=high` green.
      **Accept:** both exit 0.
- [ ] `bin/brakeman --exit-on-warn --exit-on-error` green.
      **Accept:** exits 0; a deliberate `params[:x]` interpolation makes it exit non-zero.
- [ ] `bin/check-ui-components` and `bin/check-theme` present and green.
      **Accept:** a deliberate `<button>` makes the first fail; a deliberate `#ff0000` makes the second fail.
- [ ] `bin/ci` runs the whole list; `.github/workflows/ci.yml` runs the same list.
      **Accept:** remove one step from the workflow and the difference is visible in review.
- [ ] A gate exists to prove itself: each script prints a count on success.
      **Accept:** `check-ui-components: N components, 0 violations`.

## Phase 5 — the first vertical slice (60 min)

- [ ] One route → controller → query object → payload → Inertia page → integration test.
      **Accept:** `bin/rails test` proves the route, the auth redirect and the props.
- [ ] `types.ts` updated in the same commit as the controller.
      **Accept:** `npm run check` passes.
- [ ] The page renders inside a sanctioned layout.
      **Accept:** `bin/check-ui-components` passes.
- [ ] One shared error page for 403/404/422/500.
      **Accept:** `GET /404` renders the Inertia page, not the Rails debug page.

## Phase 6 — auth and roles (60 min)

- [ ] Session-backed `Authentication` concern; `allow_unauthenticated_access` is the only way to be public.
      **Accept:** a new controller action is protected by default (a test proves the redirect).
- [ ] `User#home_path` decides the landing page per role.
      **Accept:** an integration test per role asserts the landing page.
- [ ] Deactivation enforced on every request.
      **Accept:** a test signs in a deactivated user and asserts the redirect.
- [ ] Demo accounts seeded with a password labelled demonstration-only.
      **Accept:** the README table matches the seeds constants.

## Phase 7 — data and demo (30 min)

- [ ] Seeds idempotent; the double-seed test exists.
      **Accept:** `db:seed` twice → identical counts.
- [ ] One record per workflow state, one restricted record, one internal note beside one public reply.
      **Accept:** a seeds test asserts the set of statuses equals `Model::STATUSES`.
- [ ] `RAILS_ENV=test db:seed:replant` followed by `db:test:prepare` in the CI list.
      **Accept:** repeated `bin/ci` runs stay green.

## Phase 8 — briefing (20 min)

- [ ] `AGENTS.md` (or `CONTRIBUTING.md`) with: stack, commands, architecture rules, and links to the
      constitution and the gate sources.
      **Accept:** a new contributor can name the test command after reading it once.
- [ ] A constitution with numbered principles and a version. Amendments are separate commits.
      **Accept:** the version and the date of the last amendment are at the top of the file.
- [ ] The skills pack (this repository) installed or referenced, so conventions are not re-explained.

## What "done" looks like

```bash
git clone <repo> && cd <repo>
bin/setup
bin/dev            # http://localhost:3100 renders the first page
bin/ci             # every gate green, no exceptions, no suppressions
```

Three commands, no manual steps, no "on my machine". If any of the three fails on a machine that has the
pinned Ruby, Node and Postgres, the checklist is not finished.
