# Technical risk areas

Two honest categories here, and each item is labeled which one it is. Don't blur them in a review — say
explicitly when you're citing something this codebase actually shipped unflagged vs. reasoning from the domain
with no real precedent.

## Category A: real, already-merged, unflagged findings in this exact codebase

These aren't hypothetical — they're sitting in `master` right now, merged with zero recorded review comments.
They're evidence of the *kind* of thing that gets past this repo's current process (self-merge, no textual
review), not a list of things to go fix as part of an unrelated review. Flag a **new** instance of the same
pattern in a diff under review; don't demand the author fix unrelated pre-existing code unless the PR already
touches that exact code.

1. **Secret/credential values written to `console.log`.** `lib/redisManager.ts` (added in PR #3, the TLS PR)
   logs the TLS key passphrase directly:
   ```ts
   if (argv["tls-key-passphrase"]) console.log("Using key passphrase:", argv["tls-key-passphrase"]);
   ```
   This ships in `master` today, unflagged (PR #3 has zero review comments). If a PR under review adds any new
   CLI option that carries a credential, password, key, or token, check whether it (or existing code near it)
   logs the value — this repo has real, current precedent for that exact mistake slipping through.

2. **The `x ? x : undefined` idiom silently discards an explicit falsy override.** Also in `lib/redisManager.ts`
   (PR #3):
   ```ts
   rejectUnauthorized: argv["reject-unauthorized"] ? argv["reject-unauthorized"] : undefined,
   ```
   `reject-unauthorized` defaults to `true` and is a boolean. If a user explicitly passes
   `--reject-unauthorized=false` to intentionally disable certificate validation (e.g. against a self-signed test
   server), the ternary evaluates `false ? false : undefined` → `undefined`, silently discarding their explicit
   choice. The same idiom is used for `tls-key`/`tls-cert`/`tls-ca`/`tls-key-passphrase` too, but those are
   strings defaulting to `""`, where "falsy means not provided" is actually correct — it's specifically the
   *boolean* option reusing the same idiom where it breaks. If a diff under review adds a new boolean or numeric
   CLI flag using this `value ? value : default` pattern, check whether a legitimate falsy value (`false`, `0`)
   is distinguishable from "not provided."

3. **Validation-by-`console.warn`-and-silently-coerce, not by failing.** `lib/redisManager.ts` (PR #4):
   ```ts
   if (argv["hit-rate"] > 100 || argv["hit-rate"] < 0) {
     console.warn("hit-rate can't be higher than 100 or lower than 0, setting it to the default value");
     argv["hit-rate"] = 100;
   }
   ```
   This is a real, deliberate, and arguably reasonable pattern for a CLI benchmark tool (fail soft, keep running)
   — it's not automatically wrong. But it means a typo'd flag value silently produces a *different* benchmark run
   than the user asked for rather than stopping. Worth naming (not necessarily blocking) if a new flag adds
   similar range validation but the PR's intent isn't clear on whether "warn and clamp" or "fail fast" is right
   for that particular flag.

## Category B: domain-reasoned, no real precedent in this repo — say so when you use these

This is a concurrent Redis / Redis-OSS-Cluster load generator. These are risk areas that follow from what the
code does, not from an evidenced review comment (there are none). Use them only when relevant to the diff, and
be explicit that you're reasoning from the domain, not citing a maintainer requirement:

- **Standalone vs. cluster client parity.** `lib/redisManager.ts` builds socket/client options once and reuses
  them for both `createClient` and `createCluster` paths (see the TLS diff, which threaded `socketOptions`
  through both). A new option added to only one of the two client-construction branches will silently not apply
  to the other mode — check both paths when a diff touches connection options.
- **Key-space / hit-rate math.** `keys-count`, `hit-rate`, and `key-prefix` (PR #4) drive how many keys are
  pre-populated vs. left absent, which determines the actual observed cache-hit ratio during the run. If a diff
  changes this math, check the boundary cases (`hit-rate=0`, `hit-rate=100`, `keys-count=0` or `1`) — a benchmark
  tool silently producing a workload different from what its flags claim is a correctness bug specific to what
  this tool is for, not a generic code-quality nit.
- **Concurrent-client fan-out and shutdown.** Multiple `commanderRoutine` promises run concurrently against
  shared `isRunningRef`/`totalMessagesRef` state; a new code path that touches the run/stop signaling should be
  checked for a client that never observes the stop signal (hangs) or a shared counter raced without the
  existing reference-object pattern.
- **Rate limiting and RTT measurement correctness.** If a diff touches `rateLimiter` or the RTT
  histogram/accumulator, check that the `startTime`/`endTime` measurement window still brackets only the actual
  Redis call (not queueing/rate-limiter wait time) — PR #4's refactor moved key selection outside the timed
  window specifically to keep this true; a future diff could accidentally widen the window again.

## What this file deliberately does not claim

No item above is cited as "the maintainers require this" — this repo has no recorded review comment saying so
for any of them. Category A items are cited as "this exact mistake already shipped once, unflagged"; Category B
items are cited as domain reasoning with no real precedent. Keep that distinction in the review text.
