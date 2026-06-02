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
