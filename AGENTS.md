# AGENTS.md

Guidance for AI agents (and humans) working in this repository. Read this before making changes.

## What this project is

`mackeeper-cli` is a **single-file, zero-dependency interactive storage doctor for macOS**, written in pure bash 3.2+.

- The entire tool is `bin/mackeeper` — one file, no build step, no runtime deps.
- It is a TUI: colored `tput` menus, live progress bars, interactive prompts.
- Safety is the core contract: **dry-run first, deletions go to the macOS Trash, never raw `rm`**.

## Project layout

```
bin/mackeeper          # the whole tool (single bash script)
.github/workflows/     # CI, PR validation, secret scan, stale, welcome
.github/ISSUE_TEMPLATE/
README.md  LICENSE  CHANGELOG.md  CONTRIBUTING.md  CODE_OF_CONDUCT.md  SECURITY.md
```

## Quick commands

```bash
# syntax check (must pass — uses macOS bash 3.2 semantics)
bash -n bin/mackeeper

# shellcheck (warning-level clean is the bar; error-level is enforced in CI)
shellcheck -x -s bash -S warning bin/mackeeper

# test functions WITHOUT launching the TUI (script is source-guarded)
bash -c 'source bin/mackeeper; full_report'
bash -c 'source bin/mackeeper; run_category caches dry'
bash -c 'source bin/mackeeper; run_category logs dry; run_category logs apply'

# run the real app
./bin/mackeeper
```

## Non-negotiable rules

1. **Bash 3.2 compatibility.** macOS ships bash 3.2. Never use: associative arrays, `mapfile`, `${var^^}`/`${var,,}`, `[[ =~ ]]` in ways 3.2 can't handle, `declare -A`. Test with `/bin/bash`, not just homebrew bash 5.
2. **Zero new dependencies.** No `dialog`, `whiptail`, `jq`, `python`, `yq`. Optional tools (`brew`, `docker`, `npm`, `uv`, `coursier`) must be guarded with `command -v` and must never be required.
3. **Trash, never `rm`.** All deletion goes through `/usr/bin/trash` via `trash_path()`. Permanent deletion (Empty Trash) is a separate, explicitly-confirmed action.
4. **Dry-run first.** Every category function takes a `dry|apply` mode argument. `dry` only reports (sizes + safety level); `apply` performs the action behind a `confirm()` prompt.
5. **Guard rails.** `trash_path()` must refuse `$HOME`, `/`, `..`, and `*` paths. Do not weaken these.
6. **No comments unless the WHY is non-obvious.** Small, focused functions. No premature abstraction. No dead code — delete it.
7. **Respect macOS TCC.** System caches (`com.apple.*`, `CloudKit`, `FamilyCircle`) are flagged via `is_system_cache()` and skipped unless the terminal has Full Disk Access. Keep that behavior.

## Architecture cheat-sheet (read before editing)

- **Entry guard**: the script only runs `main_menu` when executed, not when sourced (`if [ "${BASH_SOURCE[0]}" = "$0" ]`). Tests `source` it, so keep this intact.
- **`run_category <name> <mode>`** dispatches to `cat_caches`, `cat_homebrew`, `cat_uv`, `cat_npm`, `cat_coursier`, `cat_docker`, `cat_appsupport`, `cat_logs`, `cat_downloads`, `cat_trash`.
- **`trash_path <path>`** is the single deletion choke-point — it validates the path, trashes, and tracks `TRASH_OK`/`TRASH_FAIL`/`FREED_BYTES`.
- **`summary_batch <label> <bytes_before> <ok_before> <fail_before>`** prints per-batch results; call it after a batch of `trash_path` calls.
- **Progress bars**: `pbar <cur> <total> <label>` (determinate) and `spin_out <outfile> <label> -- <cmd>` (indeterminate while a slow command runs).
- **State**: profile + all-time savings persist in `~/.mackeeper/config`; actions log to `~/.mackeeper/history.log`. `mk_save_config()` persists after batches.
- **Globals** (top of file): `SCAN_ITEMS`, `SCAN_BYTES`, `FREED_ITEMS`, `FREED_BYTES`, `TRASH_OK`, `TRASH_FAIL`, `TOTAL_FREED_KB`, `FREE_BEFORE`.

## Adding a new cleanable category

1. Add `cat_<name>() { local mode="$1"; ... }` following the existing shape: header → size rows via `item_row "<level>" "<size>" "<name>"` → `confirm` in `apply` mode → `summary_batch`.
2. Register it in `run_category()` and the `submenu()` case statement.
3. Dry-run must list items before any action; apply must go through `trash_path`.
4. Update README (menu table), CHANGELOG (Unreleased → new entry), and CONTRIBUTING if behavior is user-visible.
5. Run `bash -n`, `shellcheck -S warning`, and both dry + apply modes on a throwaway `/tmp` dir.

## Testing

- CI runs: `bash -n`, shellcheck (`-S error`), a smoke test (`scan_overall`, dry-run categories, guard-rail assertions that `trash_path` returns 1 for `$HOME`, `/`, traversal paths).
- No test framework. Manual verification is via `source bin/mackeeper; <fn>`.
- When testing `apply` flows, use paths under `/tmp` — do not trash real user data. Verify Trash is restored afterward (terminal access to `~/.Trash` may be blocked by TCC; use `osascript -e 'tell application "Finder" to get name of every item of trash'`).

## Commit conventions

- Conventional Commits: `fix:`, `feat:`, `refactor:`, `docs:`, `test:`, `chore:`, `ci:`.
- PRs must target `main`; PR Validation enforces the title format in CI.
- Never include AI/model attribution in commits, PRs, or release notes.

## Release flow

- Version lives in `MK_VERSION="0.0.1-beta"` at the top of `bin/mackeeper`.
- Bump it + add a CHANGELOG entry, tag `vX.Y.Z`, then `gh release create` (set `--latest`, not draft).

## Security scope

The tool deletes files with user permissions. Anything that could cause arbitrary deletion or command injection (via filenames passed to `brew`/`docker`/etc.) is in scope. See `SECURITY.md`. Report issues privately, never in a public issue.
