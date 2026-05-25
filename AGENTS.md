# Agent guidelines

Instructions for AI coding agents (Claude Code, Copilot, Cursor, etc.) working in this repo.

## Project overview

`redis-mixed-bench` is a high-performance Redis benchmark tool written in TypeScript/Node.js. It drives concurrent mixed workloads of `SET`, `GET`, `HSET`, and `HGET` commands against a standalone Redis server or a Redis OSS Cluster, reporting throughput and HDR-histogram latency results. Multiple benchmark instances can be launched in parallel via `scripts/run-bench-mixed.sh`. The tool supports TLS, rate limiting, configurable key-space sizes, hit-rate simulation, and optional JSON result output.

## Local setup

```bash
git clone git@github.com:redis-performance/redis-mixed-bench.git
cd redis-mixed-bench
npm install
```

Requires **Node.js 18 or later** and **npm**. TypeScript is compiled with `tsc`; `ts-node` is available for running source files directly without a build step.

## Branch naming

Same as human contributors: `<type>/<short-description>` (e.g. `fix/off-by-one-in-pipeline`).

## Coding standards

- Match the style already in the file you are editing.
- TypeScript strict mode is enabled — do not weaken compiler settings.
- Use Prettier for formatting: `npm run format`.
- Prefer clear, minimal changes over large refactors unless explicitly asked.
- Do not add comments that describe *what* the code does — only add comments when the *why* is non-obvious.
- Do not introduce new dependencies without checking with the maintainer.

## Running tests

The project does not yet have a unit test suite. Validate changes by running the benchmark against a local Redis instance:

```bash
# Build TypeScript to dist/
npm run build

# Run a quick smoke test (requires Redis on localhost:6379)
node dist/bin/bench-mixed.js --clients=2 --test-time=5

# Alternatively, use ts-node via the helper script (no build step)
./scripts/run-bench-mixed.sh 1 --clients=2 --test-time=5
```

The benchmark must start successfully, print progress, and exit cleanly. Always run this smoke test before declaring a task complete.

## How to submit changes

1. Create a branch: `git checkout -b <type>/<description>`.
2. Run `npm run format` to ensure consistent formatting.
3. Commit with a clear message focused on *why*, not *what*.
4. Open a pull request against `master`.
5. Do **not** push directly to `master`.

## What to avoid

- Do not reformat files unrelated to your change (beyond running `npm run format`).
- Do not remove error handling.
- Do not commit secrets, credentials, or large binary files.
- Do not commit the `dist/` or `node_modules/` directories.
- Do not amend published commits.
