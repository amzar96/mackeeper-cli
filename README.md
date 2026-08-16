# mackeeper-cli

> A zero-dependency interactive storage doctor for macOS, written in pure bash.

![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)
![Bash](https://img.shields.io/badge/bash-3.2%2B-blue.svg)
![Platform](https://img.shields.io/badge/platform-macOS-lightgrey.svg)
![Version](https://img.shields.io/badge/version-v0.0.1--beta-orange.svg)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)

`mackeeper-cli` scans your Mac, shows exactly what is eating your disk (with safety ratings), and lets you clean it up — **always with a dry-run first, always to the Trash**. No accounts, no network calls, no system modification. One bash file, works out of the box.

## Why this exists

macOS storage mysteriously fills up. The Finder "storage" pane is vague. `du` is scary. `mackeeper-cli` gives you a guided, color-coded report of the real space hogs — caches, Homebrew, uv/npm/Coursier caches, Docker, app support blobs, logs, old downloads — and lets you reclaim space safely.

## Features

- **Interactive TUI** — colored `tput` menus and live progress bars, no `dialog`/`whiptail` dependency.
- **Dry-run by default** — every category lists items + sizes + a safety level before you act.
- **Trash-based deletion** — files go to `~/.Trash`, never permanent `rm`. Reversible.
- **Guard rails** — refuses to touch `$HOME`, `/`, `..`, or `*` paths; flags macOS system caches.
- **Curated danger zones** — Caches, Homebrew, uv, npm, Coursier, Docker (safe/deep prune), App Support, Logs, stale Downloads, and Empty Trash.
- **Session & lifetime savings** — see free space before → after, `%` gained, and all-time recovered (persisted across runs).
- **Profile & history** — your name, theme, and a log of every action in `~/.mackeeper/`.

## Requirements

- macOS (any recent version; tested on 15.x)
- `bash` 3.2+ (ships with macOS)
- `tput` (ships with macOS)
- Optional (for extra categories): `brew`, `uv`, `npm`, `coursier`, `docker`

> **Note on Full Disk Access:** macOS (TCC) blocks the terminal from trashing Apple system caches (`com.apple.*`, `CloudKit`, …). They are auto-skipped. To clear them too, grant the terminal **Full Disk Access** in *System Settings → Privacy & Security → Full Disk Access*.

## Install

### Quick (download & run)

```bash
curl -sSL https://raw.githubusercontent.com/amzar96/mackeeper-cli/main/bin/mackeeper -o mackeeper
chmod +x mackeeper
./mackeeper
```

### Manual

```bash
# copy into your PATH
mkdir -p ~/bin
cp bin/mackeeper ~/bin/
chmod +x ~/bin/mackeeper
mackeeper
```

### Or clone the repo

```bash
git clone https://github.com/amzar96/mackeeper-cli.git
cd mackeeper-cli
./bin/mackeeper
```

## Usage

Run it and follow the menu:

```
◆ mackeeper v0.0.1-beta

  [1] Full scan report
  [2] Clean a category…
  [3] Profile / history
  [4] Exit
```

| Menu | What it does |
|------|--------------|
| **Full scan report** | Disk overview, top-8 home consumers, color-coded category quick-scan |
| **Clean a category** | Caches · Homebrew · uv · npm · Coursier · Docker · App Support · Logs · old Downloads · Empty Trash |
| **Profile / history** | Name, theme, per-session and all-time savings, action log |
| **Exit** | Prints a session summary (space gained, items trashed) |

### Safety levels

| Level | Colour | Meaning |
|-------|--------|---------|
| Safe | green | Re-downloadable cache; low risk |
| Warn | yellow | App-specific data; re-created or updater leftovers |
| Risky | red | Docker prune / Empty Trash — extra confirmation required |

## Project layout

```
.
├── bin/
│   └── mackeeper          # the whole tool — a single bash script
├── .github/
│   ├── workflows/         # CI, PR validation, stale, secret scan, welcome
│   ├── ISSUE_TEMPLATE/
│   └── PULL_REQUEST_TEMPLATE.md
├── LICENSE
├── CHANGELOG.md
└── CONTRIBUTING.md
```

## Roadmap (v0.1.0 and beyond)

- [ ] Per-file drill-down inside categories
- [ ] `--noninteractive` flag for scripted/automated cleaning
- [ ] Sudo-mode for macOS system caches (with explicit opt-in)
- [ ] Localization (i18n)
- [ ] Install script + Homebrew tap

## Contributing

Contributions are welcome — bug reports, feature ideas, and PRs. Please read [CONTRIBUTING.md](CONTRIBUTING.md) first. All PRs from any contributor run through automated checks ([CI](.github/workflows/ci.yml)).

## License

MIT — see [LICENSE](LICENSE). The name "mackeeper" is used generically to describe a Mac disk-keeper tool; it is not affiliated with MacKeeper or its vendors.
