# Contributing

We treat this repo as "Open Source" within Redis: anyone who clears the bar below is welcome to contribute.

## Local setup

```bash
git clone git@github.com:redis-performance/redis-mixed-bench.git
cd redis-mixed-bench
npm install
```

Requires **Node.js 18 or later** and **npm**. TypeScript is compiled via the `tsc` build step; `ts-node` is available as a dev dependency for running without a prior build step.

## Branch naming

```
<type>/<short-description>
```

Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`

Example: `feat/add-cluster-mode`

## Coding standards

- Keep changes focused; one logical change per PR.
- Follow the conventions already present in the codebase (TypeScript strict mode, formatting via Prettier).
- No dead code, no commented-out blocks.
- Format code before committing: `npm run format`

## Submitting changes

1. Fork or create a branch from `master`.
2. Make your changes with clear, atomic commits.
3. Open a pull request against `master` with a descriptive title and summary.
4. Address review comments promptly; force-push to the same branch to update.

## Testing

The project does not yet have a unit test suite. Validate your changes by running the benchmark manually against a local Redis instance:

```bash
# Install dependencies
npm install

# Build TypeScript
npm run build

# Run a quick smoke test against Redis on localhost:6379
node dist/bin/bench-mixed.js --clients=2 --test-time=5

# Or use ts-node directly (no build step required)
./scripts/run-bench-mixed.sh 1 --clients=2 --test-time=5
```

Ensure the benchmark starts, reports progress, and exits cleanly before opening a PR.

## Review process

- At least one maintainer approval is required before merge.
- CI must be green.
- Maintainers may request changes or close PRs that do not meet the bar — this is normal and not personal.
