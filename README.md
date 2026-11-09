# Starter kit bootstrap

Scaffold a new Rails 8 + Inertia + React + Tailwind v4 application from a proven baseline — pinned versions, the app/ directory contract, four sanctioned layouts, gates wired and green on the first commit, demo accounts, and a golden path a new contributor can follow. Use when starting a project, cloning a template, or auditing whether a repo is actually set up for a team.

One skill, one repository. Part of a twelve-skill pack extracted from a production Rails 8 +
Inertia + React helpdesk monolith — the opinionated layer no public Rails or shadcn skill can know.
Version `2.0.0` tracks the source constitution.

## Install

```jsonc
// skills-lock.json
{
  "version": 1,
  "skills": {
    "starter-kit-bootstrap": {
      "source": "mahardikalgw/starter-kit-bootstrap",
      "sourceType": "github",
      "skillPath": "SKILL.md"
    }
  }
}
```

Or clone and symlink:

```bash
git clone git@github.com:mahardikalgw/starter-kit-bootstrap.git
ln -s "$PWD/starter-kit-bootstrap" .claude/skills/starter-kit-bootstrap
```

## When to activate

- Starting a project from a template.
- Auditing a repo for "is this set up for more than one person?".
- Onboarding: what must be true before a contributor's first pull request.

## What's inside

```
starter-kit-bootstrap/
  SKILL.md
  references/day-zero-checklist.md
```

`SKILL.md` is a router (kept under 250 lines) — the depth lives in `references/`, and the
copy-in code lives in `assets/`.

## Validate

```bash
./bin/check-skill
```

Checks frontmatter, the activation section, link resolution, orphaned references, script
executability, leftover placeholders, and — when the source app is checked out next to this
repository — that vendored scripts still match it byte for byte. Runs in CI on every push.

## Related skills

- [`speckit-feature-lifecycle`](https://github.com/mahardikalgw/speckit-feature-lifecycle) — specify → clarify → plan → tasks → implement → analyze → converge
- [`flat-ui-contract`](https://github.com/mahardikalgw/flat-ui-contract) — INV-1…INV-9 and THEME-1…3, with both gate scripts vendored
- [`merge-gate-parity`](https://github.com/mahardikalgw/merge-gate-parity) — one gate list in every file, a script contract, and local/CI triage
- [`postgres-pgvector-baseline`](https://github.com/mahardikalgw/postgres-pgvector-baseline) — PostgreSQL 16 + pgvector everywhere, Solid stores, the fresh-clone traps
- [`idempotent-demo-seeds`](https://github.com/mahardikalgw/idempotent-demo-seeds) — deterministic seeds that survive replanting, and the test-env pairing

## Licence

MIT — see [LICENSE](LICENSE).
