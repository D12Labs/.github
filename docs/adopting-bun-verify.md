# Adopting `bun-verify`

Derived from each repo's actual `package.json` scripts. A repo missing a script
must switch that input off — `bun run <missing>` exits non-zero and fails CI.

## Bun repos

| Repo | Scripts present | `with:` block needed |
|---|---|---|
| `d12-ai-harness` | lint typecheck test build | *(none — all defaults)* |
| `d12-core` | lint typecheck test build | *(none)* |
| `d12-starter` | lint typecheck test build | *(none)* |
| `delivery-hub-cc` | lint typecheck test build | *(none)* |
| `delivery-hub-grok` | lint typecheck test build | *(none)* |
| `nexora` | lint typecheck test | `build: false` |
| `flotion` | lint typecheck test | `build: false` — see note |
| `d12-studio` | typecheck test | `lint: false`, `build: false` |
| `d12-design` | typecheck build | `lint: false`, `test: false` |
| `d12-infra` | test e2e | `lint: false`, `typecheck: false`, `build: false` |

**flotion is not a drop-in.** Its CI is 314 lines across ten jobs (schema-drift,
boundaries, knip, test-db with a postgres service, images, terraform). The shared
workflow does not replace that. Either leave flotion alone, or call `bun-verify`
for the plain lint/typecheck/test lane and keep the specialised jobs local.

## Not candidates

`d12-devin`, `d12-ship`, `d12labs`, `monocode-fork`, `stremio-web-development` are
npm/node repos. A `node-verify.yml` sibling would be the equivalent; they also run
four different Node majors (20, 20, 22, 24), which is the same unpinned problem in
a different runtime.

## Migration order

Start with the five repos already named `verify`, because `org-main-verify`
already requires that check on them and adopting the shared workflow cannot change
which check is required:

1. `d12-starter`, `d12labs`*, `delivery-hub-cc`, `delivery-hub-grok`, `nexora`
2. Then the rest of the Bun repos.
3. Then widen `org-main-verify.json`'s repo list — **only after** a repo's `ci.yml`
   declares a job named `verify`. Adding a repo before that makes its PRs
   permanently unmergeable, because a required check that never reports never
   passes.

\* `d12labs` is npm, not Bun — it names its job `verify` but would need the node
equivalent.

## Caller template

```yaml
name: CI
on:
  pull_request:
    branches: [main]
  push:
    branches: [main]
concurrency:
  group: ci-${{ github.ref }}
  cancel-in-progress: true
jobs:
  verify:
    uses: D12Labs/.github/.github/workflows/bun-verify.yml@main
```
