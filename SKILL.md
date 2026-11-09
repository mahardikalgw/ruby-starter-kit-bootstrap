---
name: starter-kit-bootstrap
description: Scaffold a new Rails 8 + Inertia + React + Tailwind v4 application from a proven baseline — pinned versions, the app/ directory contract, four sanctioned layouts, gates wired and green on the first commit, demo accounts, and a golden path a new contributor can follow. Use when starting a project, cloning a template, or auditing whether a repo is actually set up for a team.
version: 2.0.0
---

# Starter kit bootstrap

Day zero decides the next six months. Every artifact below exists to make the **first commit** pass every
gate a mature repository has — because a gate added later is a gate that starts red and gets suppressed.

## When to activate

- Starting a project from a template.
- Auditing a repo for "is this set up for more than one person?".
- Onboarding: what must be true before a contributor's first pull request.

## 1. Pin the baseline

| Layer | Version, and why that one |
|---|---|
| Ruby | `3.4.x`, pinned in `.ruby-version` (a toolchain file, not a README line) |
| Rails | `~> 8.0` with the Solid trio |
| PostgreSQL | `16` **with pgvector**, in development, test, CI and production |
| Node | `24.x` — same major in CI and locally |
| React | `19.x` via Inertia, not a separate SPA |
| Tailwind | `4.x` CSS-first (`@theme inline`, no `tailwind.config.js`) |
| TypeScript | strict, with `npm run check` as a gate |

Versions are normative: changing one is a planned migration with a test impact, not a dependency bump.

## 2. The directory contract

```
app/
  models/       domain rules, scopes, derived labels
  controllers/  auth, params, one domain call, render
  services/     multi-step writes, each with a Result
  queries/      reads, composable relations
  jobs/         async work (Solid Queue)
  mailers/      requester-facing mail, same copy as the page
  views/        mailer templates only — pages are Inertia
app/javascript/
  pages/        one file per Inertia component
  layouts/      exactly four: app, dashboard, auth, marketing
  components/ui/       shadcn primitives (the only place a raw element may exist)
  components/workspace/ shared product blocks: header, filter bar, empty state, pagination, badges
  lib/          copy.id.ts, types.ts, format.ts, utils.ts
```

No `app/utils`, no `app/managers`, no `app/lib`. A new top-level directory is a documented decision or it
is drift.

## 3. Gates green on commit #1

```bash
bin/rubocop
bin/check-ui-components
bin/check-theme
bin/bundler-audit
npm audit --audit-level=high
bin/brakeman --quiet --no-pager --exit-on-warn --exit-on-error
bin/rails test
npm run check
```

Wire them into `bin/ci` **and** `.github/workflows/ci.yml` in the first commit, with the local runner as
the single entry point. Then add one trivial test per layer, so the suite is not empty:

```
test/models/<model>_test.rb
test/services/<domain>/<verb>_test.rb
test/controllers/<resource>_controller_test.rb
test/integration/<journey>_test.rb
```

## 4. One request path, from the first screen

- Controllers render Inertia pages with server-built props.
- No `/api` namespace; no client fetch of primary data.
- `types.ts` mirrors every prop interface; `npm run check` proves it.
- Shared props (`auth`, `flash`, `errors`, `labels`, `nav`, `meta`) come from one place.

## 5. Auth before features

- A session-backed `Authentication` concern with `allow_unauthenticated_access` as the only way to be
  public.
- One page per role decides where a user lands (`home_path`), so no controller hard-codes a redirect.
- Deactivation is enforced on every request, not only at sign-in.
- The sign-in method is the product's real one. If it is SSO, there is no password form to test.

## 6. Demo data and demo accounts

- Seeds are idempotent and deterministic (`find_or_initialize_by` on natural keys, fixed dates).
- One account per role, plus one scoped role, plus a password labelled "hanya untuk demonstrasi".
- One record in every workflow state, one restricted record, one internal note beside one public reply.
- `bin/rails db:seed` twice changes nothing; the seeds test proves it.

## 7. The golden path

A single document (README or `quickstart.md`) with:

```bash
bin/setup        # install everything, migrate, seed
bin/dev          # http://localhost:3100
bin/ci           # the same gates CI runs
```

Three commands. If onboarding needs a fourth, the fourth is the bug.

Pin the dev port where a credential depends on it (an OAuth redirect URI), and repeat the number only
where a process manager overrides it — with a comment saying why.

## 8. Agent-ready from commit #1

A repo that agents and humans share needs one briefing file, not seven:

- an `AGENTS.md` (or equivalent) that states the stack, the commands, the architecture rules and the
  reference documents — and **links** to the constitution rather than duplicating it;
- a constitution with numbered, testable principles;
- gates that a checker can run, so "the rules" are executable rather than aspirational;
- a skills pack for the conventions that are too large for the briefing (see the other skills here).

## The first-week audit

| Check | Fails when |
|---|---|
| `git clone` → `bin/setup` → `bin/dev` | any manual step, any missing env var without a documented default |
| `bin/ci` on a fresh clone | a step assumes state left by a previous run |
| `bin/rails test` twice | seeds or counters are not idempotent |
| A second developer changes a label | it is typed in three files |
| A second developer adds a page | they must read 400 lines to find the layout |
| A gate is added | it exists in one of the two CI files |

## References

- [references/day-zero-checklist.md](references/day-zero-checklist.md) — the ordered checklist with acceptance criteria
