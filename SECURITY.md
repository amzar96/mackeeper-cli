# Security Policy

## Supported versions

| Version   | Supported          |
|-----------|--------------------|
| v0.0.1-beta | Yes (active beta) |

## Reporting a vulnerability

**Do NOT open a public issue for a security vulnerability.** Please report it privately:

- Use the GitHub private vulnerability reporting flow: https://github.com/amzar96/mackeeper-cli/security/advisories
- Or email the maintainers directly (see the repo owner profile).

You can expect an acknowledgement within 48 hours and a plan/status update within 7 days. We will keep you informed of progress and credit you in the release notes (unless you prefer anonymity).

## Scope

This tool runs with your user permissions and deletes files, so its safety properties matter:

- Path validation (`trash_path` refuses `$HOME`, `/`, `..`, `*`)
- Trash-based (reversible) deletion; permanent delete only behind explicit `EMPTY`/`YES` confirmation
- No network calls, no telemetry, no external dependency execution
- Optional external tools (`brew`, `docker`, `npm`, `uv`, `coursier`) are invoked only with explicit user consent

## What we consider in-scope

- Path traversal / arbitrary deletion through crafted input
- Command injection via directory/file names passed to external tools
- Anything that bypasses the confirmations

## Out of scope

- Known macOS TCC behavior (e.g. terminal lacking Full Disk Access)
- Third-party code the tool shells out to
