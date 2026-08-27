# Project agent memory

This file is the project's committed home for project-intrinsic agent knowledge: build, test, release, architecture, and sharp-edge notes that should travel with the code.

- Add durable project-specific notes here as they are discovered through real work.
- **Stow layout:** the whole repo is one Stow package (`stow --target=$HOME .`, see README). `dot-`
  prefixed top-level entries map to `.`-prefixed home targets via the `--dotfiles` flag in
  `.stowrc`. `dot-config/*` and `dot-github/*` are merge targets: `~/.config` and `~/.github` must
  exist as real directories first so Stow symlinks individual subdirectories/files into them
  instead of replacing the whole directory (README's "First-time setup"). A new tool's config
  package (e.g. `dot-config/herdr/`) just needs its subdirectory added under `dot-config/` -
  Stow picks it up automatically via the wildcard `.` package, no bootstrap script changes needed.
- **Herdr config (`dot-config/herdr/config.toml`):** validate syntax with
  `HERDR_CONFIG_PATH=<path> herdr config check`. Herdr auto-generates its own real (non-symlink)
  `~/.config/herdr/config.toml` (just `onboarding = false`) on first run, so a machine that already
  ran Herdr before adopting this repo will hit a Stow conflict rather than a silent overwrite - see
  README's "Herdr: initial setup" for the fix (move the auto-generated file aside, then restow).
  Current scope is deliberately narrow: only `[keys] prefix` is set (to `ctrl+space`, matching
  tmux's prefix in `dot-config/tmux/tmux.conf`) - no other tmux settings have been ported over yet.

## Maintaining this file

Keep this file for knowledge useful to almost every future agent session in this project.
Do not repeat what the codebase already shows; point to the authoritative file or command instead.
Prefer rewriting or pruning existing entries over appending new ones.
When updating this file, preserve this bar for all agents and keep entries concise.
