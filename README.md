# Reproduction of [#28599](https://github.com/nrwl/nx/issues/28599)

The issue appears to be introduced in nx 19.8.4, this repro starts off
in 19.8.3 so you can see it functioning correctly before the issue.

```bash
pnpm i
# Ensure the generated files and cache are cleared
git clean -xdf ./libs
pnpm nx reset
# Run all the audit targets in the workspace, use
# a large parallel number to test task ordering
# and gating.
pnpm nx run-many -t audit --parallel 20
```

## Upgrade to 19.8.4

Now when we upgrade to nx 19.8.4 we can see the issue manifest:

```bash
pnpm nx migrate 19.8.4
pnpm i --no-frozen-lockfile

# Ensure a clean slate
git clean -xdf ./libs
pnpm nx reset

# Run audit target again with large degree of parallelism
pnpm nx run-many -t audit --parallel 20
```

_See tasks that depend on `libs/models:prebuild` executing ahead of it._

## Upgrade to 20.0.5

Now we can confirm it is still a problem in 20.0.5:

```bash
pnpm nx migrate 20.0.5
pnpm i --no-frozen-lockfile
pnpm nx migrate --run-migrations

# Ensure a clean slate
git clean -xdf ./libs
pnpm nx reset

# Run audit target again with large degree of parallelism
pnpm nx run-many -t audit --parallel 20
```
