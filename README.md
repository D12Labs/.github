# `.github`

Org-wide defaults for [D12Labs](https://github.com/D12Labs).

| Path | What it does |
|---|---|
| `profile/README.md` | Renders at `github.com/D12Labs`. **Only visible if this repo is public.** |
| `.github/workflows/bun-verify.yml` | Shared `workflow_call` CI for Bun repos. Called, not copied. |
| `docs/adopting-bun-verify.md` | Per-repo inputs and migration order. |

## Why this repo exists

Before it, every repo carried its own copy of the same CI. Nine workflow files
declared `bun-version: latest`, which meant any Bun release could break CI across
the org with no commit from us, and a green run on Monday was not reproducible on
Friday. Two more were pinned to versions that had drifted apart (1.3.13, 1.3.14).

The Bun version now lives in exactly one place. So does the step order, and the
action versions.

Related: org rulesets are applied as code from
`d12-pipeline/templates/org-rulesets/`, and `org-main-verify.json` requires a
status check named `verify` — which is why the job in `bun-verify.yml` is named
`verify` and must stay that way.
