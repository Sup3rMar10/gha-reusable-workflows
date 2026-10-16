# gha-reusable-workflows

A **reusable workflow**: CI written once (`ci-reusable.yml`) and called by other workflows with inputs, an optional secret and a returned output. One repo here, but the same file can be called from other repositories (`uses: owner/repo/.github/workflows/ci-reusable.yml@ref`).

## Pieces

| File | Role |
|---|---|
| `ci-reusable.yml` | `on: workflow_call` with typed inputs (`python-version`, `run-lint`), an optional secret, and an output `tests-passed` |
| `ci.yml` | The caller: runs the reusable workflow for Python 3.12 and 3.13, then a `report` job reads the outputs |

## What ran (2026-10-16)

Both Actions runs **succeeded**: the two reusable calls, and the `report` job printed `py312 passed=true, py313 passed=true`, so the output made it back to the caller. The secret check printed `api-token provided - false`, as no such secret exists here.

## Decisions

- **Why reusable workflows:** one place to fix or harden CI for many repos, instead of copy-pasted YAML drifting apart.
- **`secrets: inherit`** passes the caller's secrets down, convenient inside one organisation; explicit `secrets:` mappings are stricter. The secret is only checked for presence, **never printed**.
- **`permissions: contents: read`** is set in the reusable workflow, since a called workflow can have at most the caller's permissions.
- **Limits to know:** reusable workflows nest only a few levels deep, and a called workflow in a different repo must allow access to it (private repos need that setting).

## Not covered

Calling it from another repository, pinning the call to a tag or SHA, secrets that actually exist, environments, and the lint step is only a byte-compile.

MIT licensed.
