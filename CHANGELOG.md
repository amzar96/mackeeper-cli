# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.0.1-beta] - 2026-08-16

Initial open-source release.

### Added

- Interactive `tput`-based menu (no `dialog`/`whiptail` dependency)
- Full scan report: disk overview, top-8 home consumers, category quick-scan
- Curated danger zones: Caches, Homebrew, uv, npm, Coursier, Docker, App Support, Logs, stale Downloads, Empty Trash
- Dry-run everywhere; deletions go to the macOS Trash (reversible)
- Safety guard rails: refuses `$HOME`, `/`, `..`, and `*` paths
- macOS system-cache detection (auto-skip; flagged unless terminal has Full Disk Access)
- Live progress bars and spinner while scanning
- Session summary on exit: free-space before/after, `%` gained, items trashed
- Persistent profile & all-time savings (`~/.mackeeper/config`)
- Action history log (`~/.mackeeper/history.log`)
- Theme selection (cyan / green / magenta / blue)
