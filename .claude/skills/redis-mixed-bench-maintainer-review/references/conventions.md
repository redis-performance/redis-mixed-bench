# Real, written conventions + observed PR patterns

Everything in this file is either (a) a direct quote from this repo's own `AGENTS.md` or `CONTRIBUTING.md`
(both added in PR #5, May 2026), or (b) a pattern actually observed in all four of this repo's real feature
PRs (#1–#4, May 2025). Nothing here is inferred or imported from another repo.

## Project shape (from `AGENTS.md`)

> `redis-mixed-bench` is a high-performance Redis benchmark tool written in TypeScript/Node.js. It drives
> concurrent mixed workloads of `SET`, `GET`, `HSET`, and `HGET` commands against a standalone Redis server or a
> Redis OSS Cluster, reporting throughput and HDR-histogram latency results.

Requires Node.js 18+; built with `tsc`, run directly via `ts-node` or the compiled `dist/`.

## Written coding standards (`AGENTS.md`)

- "Match the style already in the file you are editing."
- "TypeScript strict mode is enabled — do not weaken compiler settings."
- "Use Prettier for formatting: `npm run format`."
- "Prefer clear, minimal changes over large refactors unless explicitly asked."
- "Do not add comments that describe *what* the code does — only add comments when the *why* is non-obvious."
- "Do not introduce new dependencies without checking with the maintainer."
- "Do not remove error handling."
- "Do not commit secrets, credentials, or large binary files."
- "Do not commit the `dist/` or `node_modules/` directories."

`CONTRIBUTING.md` restates most of this and adds: "Keep changes focused; one logical change per PR" and "No dead
code, no commented-out blocks."

## Testing (written, and explicitly honest about its own gap)

Both files say, nearly verbatim: **the project does not yet have a unit test suite.** Validation is a manual
smoke test — build, then run the benchmark against a local Redis instance for a few seconds and confirm it
starts, prints progress, and exits cleanly (`node dist/bin/bench-mixed.js --clients=2 --test-time=5`, or via
`./scripts/run-bench-mixed.sh`).

This is a real, current, written decision by this project — not a review skill's job to relitigate. Don't ask a
PR to add a test suite the project has said it doesn't have. Do note, plainly, when a behavior-changing PR gives
no indication the manual smoke test was run at all.

## Review process (written, `CONTRIBUTING.md`)

- "At least one maintainer approval is required before merge."
- "CI must be green."
- "Maintainers may request changes or close PRs that do not meet the bar."

In practice, across the 5 real merged PRs, this process has never yet produced a written review comment (see
`SKILL.md`'s honesty warning) — the "at least one approval" bar has been met by a same-day/same-hour approval
(PR #5) or by the author self-merging without a recorded formal review at all (PRs #1–#4, all pre-dating today's
branch-protection setup as far as the visible history shows).

## The one real, repeated behavioral pattern: README + TODO stay in lockstep

This is the single concrete pattern that shows up in **all four** real PRs, not just one:

- PR #1 ("cleanup benchmark"): touched `README.md` alongside the code cleanup.
- PR #2 ("removed pubsub bench references, changed node redis to 4.7.0"): updated `README.md` and `TODO` to match.
- PR #3 ("Added the option to enable TLS"): added a new "TLS/SSL Configuration" section to `README.md`'s options
  list, and removed the now-done TLS line from `TODO`.
- PR #4 ("...option to configure the hit-rate"): added a new "Key Distribution and Hit Rate" section to
  `README.md`, and cleared the now-done `TODO` entry.

Every real PR that added a CLI flag added it to `README.md`'s "Available Options" section in the same commit or
PR, and every real PR that finished a `TODO` item deleted that line from `TODO` in the same PR. Treat a new
user-facing flag with no corresponding `README.md` update as a real, evidenced deviation from this repo's own
established (if informal) practice — this is the strongest, best-evidenced thing this skill can point to.

## What this repo's history does NOT give you

- No evidenced reviewer "voice" — nobody's, not fcostaoliveira's, not paulorsousa's. Don't manufacture one.
- No evidenced precedent for how this repo handles a *contested* or *rejected* PR — every merged PR here was
  accepted, and no PR has ever been closed unmerged in the visible history.
- No issue-triage precedent — zero issues have ever been opened on this repo.
- No CI-gate precedent beyond the written "CI must be green" line — the real PRs merged fast enough that it's
  not visible from PR metadata alone whether CI ran and gated anything.
