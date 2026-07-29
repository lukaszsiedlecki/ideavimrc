# ideavimrc

My personal `~/.ideavimrc` — IdeaVim configuration for JetBrains IDEs.

Built up in stages starting from the
[dbilici/IdeaVim](https://github.com/dbilici/IdeaVim) reference config,
then extended with a which-key powered leader-key layout, every native Vim
text-editing operator worth having, and a survey of ~40 other people's
`.ideavimrc` files plus the official
[IdeaVim Plugins wiki](https://github.com/JetBrains/ideavim/blob/master/doc/IdeaVim%20Plugins.md)
for IntelliJ actions and bundled extensions worth binding.

## Usage

`~/.ideavimrc` is a symlink to the `.ideavimrc` file in this repo — editing
either path edits the same file, so there's nothing to keep in sync
manually. Only `git add`/`git commit` here still need to happen by hand to
snapshot a change into history.

On a new machine, recreate the symlink:

```sh
ln -sf ~/repo/ideavimrc/.ideavimrc ~/.ideavimrc
```

Reload inside the IDE with `<leader>vr` (mapped to
`IdeaVim.ReloadVimRc.reload`). Any line starting with `sethandler` needs a
**full IDE restart** instead — reloading the config alone isn't reliably
enough for those to take effect.

## Requirements

- [IdeaVim](https://plugins.jetbrains.com/plugin/164-ideavim)
- [Which Key Lazy](https://plugins.jetbrains.com/plugin/30446-which-key-lazy) — leader-key popup
- [IdeaVim-Quickscope](https://plugins.jetbrains.com/plugin/19417-ideavim-quickscope)
- [IdeaVim-EasyMotion](https://plugins.jetbrains.com/plugin/13360-ideavim-easymotion/) + [AceJump](https://plugins.jetbrains.com/plugin/7086-acejump/)
- [Case Conversion](https://plugins.jetbrains.com/plugin/10985-case-conversion) — powers the `<leader>c` Visual-mode case-conversion group

`.whichkey-lazy.json` in this repo holds label overrides for which-key
entries that Which Key Lazy can't describe on its own (see comments in
`.ideavimrc` for which mappings need this — mainly Visual-mode leader
groups and a few Vim-native extension mappings it can't introspect). Which
Key Lazy always reads this file from the user's home directory, so symlink
it there the same way as `.ideavimrc`:

- **Linux/macOS**: `ln -sf ~/repo/ideavimrc/.whichkey-lazy.json ~/.whichkey-lazy.json`
- **Windows** (PowerShell, as Administrator): `New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.whichkey-lazy.json" -Target "$env:USERPROFILE\repo\ideavimrc\.whichkey-lazy.json"`

Adjust the source path in either command if this repo isn't cloned to
`~/repo/ideavimrc`.

## What's inside

The file is organized top to bottom in the order it's actually read; each
section is commented inline (every option/mapping explains what it does,
not just the non-obvious ones — this file doubles as a way to actually
learn what each line is for).

### Options

- **Universal** — line numbers, relative numbers, scroll offset, search
  behavior (`ignorecase`/`smartcase`/`incsearch`/`hlsearch`/`gdefault`),
  command history. Nothing IDE-specific; would work in plain Vim/Neovim too.
- **IDE-only** — `clipboard` (system clipboard + IDE-aware paste),
  `ideajoin` (smart `J`), `ideamarks` (Vim marks show as IDE bookmarks),
  `idearefactormode` (stay in Vim mode during IDE rename/refactor).

### Native text-editing operators (bundled in IdeaVim, no extra plugin)

| `set` | Adds | Example |
|---|---|---|
| `surround` | `ys`/`cs`/`ds` + `S` (Visual) | `ysiw"` wraps a word in quotes |
| `commentary` | `gcc`, `gc{motion}` | `gcc` toggles a line comment |
| `ReplaceWithRegister` | `gr{motion}`, `grr` | replace text with a register without clobbering it |
| `exchange` | `cx{motion}` (twice) | swap two arbitrary pieces of text |
| `argtextobj` | `ia`/`aa` text object | `dia` deletes a function argument |
| `matchit` | extends `%` | jumps between `if`/`else`/`end`, tags, not just brackets |
| `highlightedyank` | — | flashes a highlight on whatever was just yanked |
| `textobj-entire` | `ae`/`ie` text object | `vae` selects the whole buffer |
| `textobj-indent` | `ai`/`ii` text object | `dai` deletes a same-or-greater-indent block |
| `functextobj` | `am`/`aM`/`im` text object | `dam` deletes a whole method, `cim` its inner body |
| `classtextobj` | `ac` text object | `dac` deletes a whole class definition |
| `targets` | more `di(`/`ci"`-style variants | `cin)` changes inside the *next* parens without moving there first |
| `mini-ai` | `aq`/`iq`/`ab`/`ib` text object | `dib` deletes inside whichever bracket type is nearest |
| `indentwise` | `[-`/`]-`/`[+`/`]+`/`[%`/`]%` motions | `]%` jumps to the end of the current indent block |
| `CamelCaseMotion` | `\w`/`\b`/`\e`/`\ge` motions | `\w` on `getUserAccountBalance` hops `User → Account → Balance` |

`CamelCaseMotion` needs `g:camelcasemotion_key` set *before* `set
CamelCaseMotion` — bound to `\` (the pre-space-leader default, otherwise
unused here) instead of `<leader>` since `<leader>w`/`<leader>b` are already
the Window/Bookmarks groups below.

Two more require a separate JetBrains Marketplace plugin (see
Requirements): `quickscope` (always-on `f`/`F` target highlighting) and
`easymotion` (`<leader><leader>{motion}` label-jump navigation).

### IDE-wide extras

- `VimEverywhere` — reuses the AceJump plugin already required for
  `easymotion` above, no separate install. `Ctrl-Shift-\` overlays letter
  hints on any clickable UI element (buttons, tool window tabs, tree
  nodes...); also makes NERDTree's `o`/`t`/`T`/`s`/`i` mappings work in any
  focused tree, and enables Vim window-motions inside tool windows.
- `youcompleteme` — no extra plugin. While the code-completion popup is
  open, `Tab`/`Shift-Tab` cycle through its items instead of doing their
  normal job (insert a tab / dedent); falls back to normal behavior
  automatically once the popup closes.

### Raw (non-leader) key mappings

Pane/split navigation (`Ctrl-h/j/k/l`), jumplist (`Ctrl-o`/`Ctrl-i` →
IDE Back/Forward), method navigation (`[[`/`]]`), semantic selection
expand/shrink (`Alt-Up`/`Alt-Down`), line vs. statement move
(`Alt-j/k` for a single line, `Ctrl-Shift-Up/Down` for a whole PSI-aware
statement), multi-cursor (`Alt-n/p/a` by matching text, `Ctrl-Alt-Up/Down`
by fixed screen position), and a tool-windows switcher popup
(`Ctrl-Shift-M`).

Several of these needed a `sethandler` line because IntelliJ's own default
keymap already claims that shortcut for something else (documented inline
at each one, along with what it's overriding).

### Leader-key groups (`<leader>` = Space)

| Prefix | Group | Entries |
|---|---|---|
| `<leader>g` | Goto | 7 |
| `<leader>s` | Search | 8 |
| `<leader>f` | Files | 13 |
| `<leader>w` | Window | 12 |
| `<leader>r` | Run | 8 |
| `<leader>d` | Debugging | 13 |
| `<leader>l` | Language / refactor | 19 |
| `<leader>t` | Tabs | 18 |
| `<leader>D` | Display | 2 |
| `<leader>i` | Information | 13 |
| `<leader>b` | Bookmarks | 7 |
| `<leader>v` | IdeaVim (edit/reload config) | 2 |
| `<leader>c` | Case Conversion (Visual mode) | 12 |
| `<leader>G` | Git | 14 |
| `<leader>m`/`T`/`B`/`W` | Misc (popup menu, terminal, database, hide-all) | 4 standalone |

Every group has a `g:WhichKeyDesc_*` label per entry (not just one per
group) so the which-key popup is fully readable — press `<leader>` and
read, instead of memorizing this table.
