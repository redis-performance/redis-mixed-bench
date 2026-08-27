---
name: redis-mixed-bench-maintainer-review
description: Review a redis-performance/redis-mixed-bench pull request, branch, or diff the way this specific repo's real (very thin) history actually works — grounded in its written AGENTS.md/CONTRIBUTING.md rules and the four real merged PRs on record, not a fabricated maintainer "voice" or invented precedent. Use this whenever the user asks to review a redis-mixed-bench PR, asks whether it would pass real review here, or wants a pre-merge check specific to this repo. Prefer this over a generic code-review skill for anything touching redis-performance/redis-mixed-bench — the generic skill doesn't know this project's TypeScript/Node conventions or its actual (thin) review record.
---

# redis-mixed-bench maintainer-style review

## Honesty warning — read this before writing anything

**This repo's real review history is extremely thin, and you must say so rather than invent a richer one.**
At the time this skill was written, `redis-performance/redis-mixed-bench` has exactly five merged PRs, ever:

- PRs #1–#4 (May 2025): all from the repo's sole external contributor, **htemelski** (Hristo Temelski) — who
  also authored the initial commit. Every one of these PRs has an **empty PR description**, **zero recorded
  GitHub reviews**, and **zero review comments**. Each was opened and self-merged within minutes to a few hours.
  There is no maintainer prose to mine a "voice" from here — none exists.
- PR #5 (May 2026): an unrelated, org-wide bulk rollout that added this repo's `CONTRIBUTING.md` and `AGENTS.md`
  files. It has exactly one recorded review: an `APPROVED` from **paulorsousa** (a repo admin) with a **blank
  body** — no written comment at all.

No issue has ever been opened on this repo. There is no "maintainer personality" evidenced anywhere in this
history — not fcostaoliveira's, not paulorsousa's, not anyone's. **Do not imitate a reviewer voice this repo has
no record of.** If you're tempted to write something like "the maintainers typically say X," stop — you do not
have that citation. Say plainly that this repo's history doesn't give you one, and reason about the diff on its
own technical merits instead.

What you *do* have, and should actually use:

1. **`references/conventions.md`** — the real, explicit, written rules from this repo's own `AGENTS.md` and
   `CONTRIBUTING.md` (added in PR #5), plus the one genuinely observed behavioral pattern across all four real
   PRs (README and TODO kept in lockstep with every code change). These are real and citable — quote them.
2. **`references/risk-areas.md`** — technical risk areas grounded in two honest sources: (a) specific,
   already-merged code in this exact codebase that nobody's review caught (a logged TLS passphrase, a
   truthy-ternary that silently drops an explicit `false`), cited with file/line-era context; and (b) this
   codebase's own domain (a concurrent Redis/Redis-Cluster load generator) reasoned about from first principles
   where no real precedent exists. Each item says which kind it is — don't blur the two.

## Process

1. **Get the material.** `gh pr view <n> --repo redis-performance/redis-mixed-bench --json body,commits,files,author`
   and `gh pr diff <n> --repo redis-performance/redis-mixed-bench`. `gh pr list --author <login> --state merged
   --repo redis-performance/redis-mixed-bench` for author-trust context — but note that with a total of 5 merged
   PRs and 2 real contributors, "author trust" here is close to meaningless as a statistical signal; don't
   overstate it.

2. **Scope gate.** This is a small TypeScript/Node.js CLI (`lib/`, `bin/`, `scripts/`) with no other subsystems.
   If a PR touches something totally outside that (e.g. a vendored asset, an unrelated file dropped in by
   mistake), say so in one sentence rather than force-fitting the checklist below.

3. **Check against `references/conventions.md`.** These are real, written, current rules — not inferred. Concretely:
   - Does the diff match the style already in the touched file (per `AGENTS.md`)?
   - Does it introduce a new dependency without a stated reason (`AGENTS.md`: "do not introduce new dependencies
     without checking with the maintainer")?
   - Is TypeScript strict mode respected — no `any`-laundering or weakened compiler settings?
   - Given there's no unit test suite (an explicit, honest admission in both `CONTRIBUTING.md` and `AGENTS.md`,
     not a gap to scold the author for), does the PR description or diff show any evidence the manual smoke test
     (`node dist/bin/bench-mixed.js --clients=2 --test-time=5` or the `run-bench-mixed.sh` equivalent) was run?
     Don't demand a test suite that the project has explicitly decided not to have yet; do note if a
     behavior-changing PR gives no indication it was run at all.
   - Does the PR keep `README.md` (and, if relevant, `TODO`) in sync with the change? All four real PRs on this
     repo did this every single time — it's the one concrete, repeated pattern the actual history shows. A PR
     that adds a new CLI flag without touching `README.md`'s options list is diverging from the only real
     precedent this repo has.

4. **Work the risk checklist** in `references/risk-areas.md`, applying the items relevant to what the diff
   actually touches (TLS/socket options, key generation and hit-rate math, cluster vs. standalone client
   construction, concurrent client fan-out, CLI arg parsing/validation). Don't apply an item to a diff it has
   nothing to do with.

5. **Write the review.** Since there's no evidenced voice to imitate, default to a plain, direct, short review:
   - Lead with what the diff does and whether it's safe/consistent with the conventions above.
   - Name concrete findings by file and behavior ("in `lib/redisManager.ts`, the new `--foo` option is read but
     never validated against X") — not abstract categories.
   - If nothing of substance stands out, say so briefly rather than manufacturing nitpicks. A one- or two-line
     "looks consistent with the existing conventions, no concerns" is a legitimate, honest output for a small
     clean diff — it's also the closest thing this repo's real history has to a norm (silence/no-comment on
     routine work was the actual observed outcome on 4 of 5 real PRs).
   - Do not fabricate a "maintainer requires this" citation. If you're drawing on `references/risk-areas.md`'s
     domain-reasoning items rather than something evidenced in this repo's real history, say so plainly (e.g.
     "I don't have a precedent in this repo for this, but...").
   - Never literally `@`-mention any GitHub username.
   - Don't write a formal essay with headers like "Correctness" / "Security" / "Performance". Short, numbered or
     plain-prose points are more honest to this repo's (thin, informal) actual scale.

6. **Land on a verdict.** Given the real history is "self-merge, no formal review text," don't manufacture a
   heavier process than exists. A short "looks fine" or a short list of concrete concerns is enough. Never write
   the literal word "Verdict," never a bolded summary line, never a "TL;DR" block — end in plain prose.

## What NOT to do

- Don't invent a reviewer voice, a "the maintainers usually..." pattern, or a richer institutional review culture
  than the five real merged PRs and zero real review comments actually show.
- Don't claim a repo convention exists unless it's either written down in `AGENTS.md`/`CONTRIBUTING.md` or
  actually observed across the real PRs (see `references/conventions.md`) — don't borrow conventions from other
  redis-performance repos (e.g. redisbench-admin's Python/pytest norms) that don't apply to this TypeScript CLI.
- Don't demand a unit test suite this project has explicitly and knowingly decided not to build yet.
- Don't literally `@`-mention any GitHub username, ever.
- Don't close with a labeled, bolded verdict block — end in plain prose.
