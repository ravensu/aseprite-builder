# AGENTS.md

## Cursor Cloud specific instructions

### Repository overview

This is a **CI/CD-only** repository (`aseprite-builder`) containing GitHub Actions workflow YAML files that automate building [Aseprite](https://github.com/aseprite/aseprite) from source. There is no application source code, no package manager, no tests, and no local services to run.

Repository contents:
- `.github/workflows/build_and_release.yaml` — Main build workflow (Windows, Ubuntu, macOS ARM)
- `.github/workflows/build_and_release_macOS_x86.yaml` — macOS x86 variant
- `BuildLog.md` — Tracks which versions have been built
- `README.md` / `LICENSE`

### Linting

Since the repo only contains YAML workflow files, the relevant "lint" and "test" tools are:

- **yamllint**: `yamllint -d relaxed .github/workflows/` — YAML syntax/style checks (warnings only; the existing files have minor style issues like indentation and line length that are not errors)
- **actionlint**: `actionlint .github/workflows/*.yaml` — GitHub Actions-specific linting (catches issues like outdated action versions and undefined matrix properties)

Both tools must be on `$PATH`. They are installed to `$HOME/.local/bin` (yamllint) and `$HOME/go/bin` (actionlint). Ensure `PATH` includes these directories:

```
export PATH="$HOME/.local/bin:$HOME/go/bin:$PATH"
```

### Known actionlint findings in existing code

- `actions/checkout@v2` is flagged as outdated (should be `@v4`)
- `matrix.build_type` is referenced but not defined in the matrix (the `BUILD_TYPE` env var is used instead)
- `macOS-13` runner label triggers a warning (it is valid but not in actionlint's default known-labels list)

These are pre-existing issues in the upstream code and should not block workflow edits.

### How workflows are triggered

Workflows run via `workflow_dispatch` (manual) or on push to `main` when `BuildLog.md` is modified. They cannot be tested locally — they run exclusively on GitHub Actions runners.
