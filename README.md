# dotfiles

Managed with [GNU Stow](https://www.gnu.org/software/stow/). The `--dotfiles` flag (set in `.stowrc`) maps `dot-` prefixed names to their `.` equivalents in the target (e.g. `dot-zshrc` → `~/.zshrc`).

For directories that should be merged into an existing target dir (like `~/.config` or `~/.github`), the target directory must exist as a real directory before stowing — stow will then symlink individual contents rather than the whole directory.

## First-time setup on a new machine

```sh
cd ~/repos/dotfiles

# Create any target directories that should be merged (not symlinked wholesale)
mkdir -p ~/.config ~/.github

# Stow everything
stow --target=$HOME .
```

## Adding or updating dotfiles

After adding new files or packages to the repo:

```sh
cd ~/repos/dotfiles
stow --target=$HOME --restow .
```

## tmux: reboot persistence

`dot-config/tmux/tmux.conf` declares `tmux-plugins/tmux-resurrect` and `tmux-plugins/tmux-continuum` alongside the other TPM plugins. On a fresh machine, install them the same way as any other declared plugin: start tmux, then press `prefix + I` to have TPM fetch and install everything.

Layout auto-restores on tmux start (`@continuum-restore on`) and auto-saves every 15 minutes (`@continuum-save-interval 15`). You can also save manually with `prefix + Ctrl-s` and restore manually with `prefix + Ctrl-r`.

Note: this restores session/window/pane structure and working directories, not literally-resumed running processes - a long-running foreground command does not keep running across the reboot itself.
