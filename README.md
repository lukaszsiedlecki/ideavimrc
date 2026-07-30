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

## Q&A

Running log of one-off questions about this config or general IDE/Vim
navigation, answered as they came up. Newest entries go at the bottom.

**How does the letter-overlay jump plugin work (AceJump/EasyMotion)?**
`<leader><leader>{motion}` (`set easymotion`, `.ideavimrc:297`) labels every
on-screen target for that motion with a letter overlay; typing the letter
jumps the cursor straight there. `VimEverywhere` (`Ctrl-Shift-\`,
`.ideavimrc:303`) reuses the same AceJump plugin to label clickable UI
elements (buttons, tool window tabs, tree nodes) instead of text targets.

**How does quickscope work, and what's the difference between `f`/`F` and
`t`/`T`?**
`quickscope` (`.ideavimrc:292`) highlights a unique target character in
every word on the line to help aim `f`/`F`/`t`/`T` — it's purely a visual
aid, the actual jump is native Vim. `f`/`t` search forward, `F`/`T` search
backward; `f`/`F` land *on* the matched character, `t`/`T` land just
*before* it (in the direction of travel). Mnemonic: `f` = find (on the
char), `t` = till (up to, not onto, the char).

**Standing on a class usage (e.g. cursor on `Payment` in `List<Payment>`),
how do I jump into that class?**
`<leader>gd` → `GotoDeclaration` (`.ideavimrc:320`). Related: `<leader>gy`
→ `GotoTypeDeclaration` (`.ideavimrc:326`) when standing on a *variable*
and wanting its type's declaration instead of the variable's; `<leader>gi`
→ `GotoImplementation` (`.ideavimrc:322`) when the declaration is an
interface/abstract method and you want a concrete implementation instead.

**How do I jump back to where I was before a Goto-style jump?**
`Ctrl-o` → `Action(Back)` / `Ctrl-i` → `Action(Forward)`
(`.ideavimrc:110-112`), mirroring native Vim's jumplist direction. `Ctrl-i`
shares a keycode with `Tab`, which is why this pair needed a `sethandler`
(`.ideavimrc:107-108`) — remember that `sethandler` lines need a full IDE
restart to take effect, not just `<leader>vr`.

**How do I search for something only inside the current class?**
Plain Vim `/pattern` already scopes to the current buffer, and one file is
one class in Java/Kotlin, so that's usually enough. For "every usage of
the symbol under the cursor, but only in this file" (as opposed to the
whole-project `<leader>su`/`<leader>iu`), use `<leader>iH` →
`HighlightUsagesInFile` (`.ideavimrc:722`), then `<leader>in`/`<leader>iN`
(`.ideavimrc:724,726`) to step between the highlighted occurrences.

**Do I have an IntelliJ "Find in Files" equivalent?**
Two options: `<leader>fc` → `FindInPath` (`.ideavimrc:392`), the classic
standalone dialog with full scope/regex/case-sensitivity controls; and
`<leader>st` → `TextSearchAction` (`.ideavimrc:359`), the newer unified
Search Everywhere "Text" tab.

**Standing on a method call (e.g. cursor on `foo` in `payment.foo()`), how
do I jump to its implementation?**
Start with `<leader>gd` (`GotoDeclaration`, `.ideavimrc:320`) — if
`Payment` is a concrete class this lands you directly in the method body.
If it lands on a bare signature instead (because `foo()` is declared on an
interface/abstract class), use `<leader>gi` (`GotoImplementation`,
`.ideavimrc:322`) to pick or jump straight to the concrete implementation.
