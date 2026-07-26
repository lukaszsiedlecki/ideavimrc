# ideavimrc

My personal `~/.ideavimrc` — IdeaVim configuration for JetBrains IDEs.

Built up in stages starting from the
[dbilici/IdeaVim](https://github.com/dbilici/IdeaVim) reference config,
with a which-key powered leader-key layout, native Vim text-editing
operators (surround, commentary, exchange, argtextobj, matchit,
highlightedyank, ReplaceWithRegister), and IntelliJ action bindings for
navigation, refactoring, git, debugging, and window/tab management.

## Usage

Symlink into place:

```sh
ln -sf ~/repo/ideavimrc/.ideavimrc ~/.ideavimrc
```

Reload inside the IDE with `<leader>vr` (mapped to
`IdeaVim.ReloadVimRc.reload`), or a full IDE restart if a `sethandler` line
changed.

## Requirements

- [IdeaVim](https://plugins.jetbrains.com/plugin/164-ideavim)
- [Which Key Lazy](https://plugins.jetbrains.com/plugin/30446-which-key-lazy) — leader-key popup
- [IdeaVim-Quickscope](https://plugins.jetbrains.com/plugin/19417-ideavim-quickscope)
- [IdeaVim-EasyMotion](https://plugins.jetbrains.com/plugin/13360-ideavim-easymotion/) + [AceJump](https://plugins.jetbrains.com/plugin/7086-acejump/)

Optional companion file (not tracked here, lives outside this repo):
`~/.whichkey-lazy.json` — label overrides for which-key entries that
Which Key Lazy can't describe on its own (see comments in `.ideavimrc` for
which mappings need this).
