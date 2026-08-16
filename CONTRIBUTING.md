# Contributing to mackeeper-cli

Thanks for wanting to contribute! This is a small, focused tool and we want to keep it that way. Here is how to help out well.

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). By participating you agree to uphold it. Harassment of any kind is not tolerated.

## What we're looking for

- **Bug reports** — clear steps to reproduce, expected vs actual output, your macOS version and bash version.
- **Feature ideas** — open an issue first to discuss, especially before big changes.
- **Pull requests** — bug fixes, docs, tests, small improvements. Always welcome.

## Ground rules (read before PRing)

This tool runs as **root-ish power on your machine's files**, so safety is the #1 priority:

1. **Dry-run first, always.** New cleaning actions must show what they would do before deleting.
2. **Trash, never `rm`.** Deletion must go through `/usr/bin/trash` or an equally reversible mechanism. Permanent deletion needs an explicit, extra-confirm step.
3. **No new dependencies.** Pure bash 3.2+ only. No `jq`, no `dialog`, no `whiptail`, no Python. If something needs an optional tool (brew, docker…), guard it with `command -v`.
4. **Compatible with macOS bash 3.2.** No associative arrays, no `mapfile`, no `${var^^}`. Test with `/bin/bash` (3.2) not just newer homebrew bash.
5. **No comments unless the WHY is non-obvious.** Keep it readable, small functions, no premature abstraction.
6. **Update the README and CHANGELOG** if you change user-visible behavior.

## Development setup

```bash
git clone https://github.com/amzar96/mackeeper-cli.git
cd mackeeper-cli

# syntax check (works on macOS bash 3.2)
/bin/bash -n bin/mackeeper

# run shellcheck if you have it (brew install shellcheck)
shellcheck -x -s bash bin/mackeeper

# source the script to test functions without launching the TUI
/bin/bash -c 'source bin/mackeeper; full_report'
```

## Testing your changes

- Run `bash -n bin/mackeeper` — must pass.
- Run every category in **dry-run** mode: `source bin/mackeeper; run_category caches dry`.
- Exercise at least one **apply** path against a throwaway directory in `/tmp` to confirm the Trash flow and the batch summary.
- The CI runs exactly these checks (see `.github/workflows/ci.yml`), including shellcheck (installable via brew, but **never required** to be a hard fail on macOS 3.2).

## Pull request process

1. Create a branch off `main`: `git checkout -b fix/your-fix`.
2. Make focused commits with clear messages (see style below).
3. Push and open a PR. Use the PR template — fill in the checklist.
4. CI must pass. A maintainer will review; be ready to iterate.
5. After approval and merge, the change is released per [CHANGELOG](CHANGELOG.md).

### Commit message style

Use the [Conventional Commits](https://www.conventionalcommits.org/) prefix:

- `fix:` bug fixes
- `feat:` new functionality
- `refactor:` internal restructuring, no behavior change
- `docs:` documentation only
- `test:` tests
- `chore:` tooling, CI, maintenance

Example: `fix: guard seq loop in pbar at 0% progress`

## Fork + PR workflow

We welcome external contributors:

1. Fork the repo on GitHub.
2. Clone your fork and add the upstream remote.
3. Create a branch, make your change, push to **your fork**.
4. Open a PR from your fork's branch to `amzar96/mackeeper-cli:main`.
5. PRs from forks run in an isolated environment with **no access to secrets**. Don't try to work around that.

## Issue templates

Use the provided templates when opening issues — bug reports and feature requests have separate forms in `.github/ISSUE_TEMPLATE/`.

## Maintainers

- [@amzar96](https://github.com/amzar96)

If your PR is approved but unmerged after a reasonable time, feel free to ping in the PR thread.
