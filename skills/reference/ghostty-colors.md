# Ghostty Color Configuration (Practical Reference)

Sources: [config docs](https://ghostty.org/docs/config), [config reference](https://ghostty.org/docs/config/reference), [official theme docs](https://ghostty.org/docs/features/theme). Retrieved 2026-10-28; trust the local `ghostty +version` and each action's `--help`.

## Full Color Template

`key = value`; key names are case-sensitive; comments must be on their own lines. The sample values work as a workflow starting point:

```conf
# Base
foreground = #d8dee9
background = #202838
# Cursor
cursor-color = #d8dee9
cursor-text  = #202838
# Selection
selection-foreground = #ffffff
selection-background = #49566d
# ANSI 0-15 (palette supports 0-255; write all of 0-15 first)
palette = 0=#202838
palette = 1=#e06c75
palette = 2=#98c379
palette = 3=#e5c07b
palette = 4=#61afef
palette = 5=#c678dd
palette = 6=#56b6c2
palette = 7=#d8dee9
palette = 8=#596579
palette = 9=#ff7b86
palette = 10=#b5e890
palette = 11=#ffd68a
palette = 12=#7fc4ff
palette = 13=#e2a6ff
palette = 14=#8be9f0
palette = 15=#ffffff
```

A new config with no `theme =` and no `config-file` depends on no installed theme.

## Key Mapping (Ghostty ↔ Kitty)

| Meaning | Ghostty | Kitty |
|---|---|---|
| Text / background | `foreground`, `background` | same names |
| Cursor / cursor text | `cursor-color`, `cursor-text` | `cursor`, `cursor_text_color` |
| Selection | `selection-foreground`, `selection-background` | `selection_foreground`, `selection_background` |
| ANSI index | `palette = N=#RRGGBB` | `colorN #RRGGBB` |

`palette` supports 0–255; programs may also emit true color directly, bypassing the palette.

## Config Discovery and Precedence

1. Version: `ghostty +version`.
2. Locations and order (since 1.2.3 the file is named `config.ghostty`; before that, `config`):
   - `$XDG_CONFIG_HOME/ghostty/config.ghostty`, then `$XDG_CONFIG_HOME/ghostty/config` (without XDG set: `~/.config/ghostty/`);
   - macOS additionally loads `~/Library/Application Support/com.mitchellh.ghostty/config.ghostty` and `.../config`, after the XDG files;
   - when multiple files exist they load in the order above; later files override earlier conflicting keys.
3. Nesting and theme override rules (the key to standalone configs):
   - `theme = <name>` loads first; same-named options in the main config override the theme; themes may contain non-color options — do not load untrusted themes;
   - files pulled in by `config-file = <path>` load **after the entire parent config file** — even colors written at the end of the parent get overridden by nested files; nested files may include further files; relative paths resolve against the file containing the directive; a `?` prefix marks the file optional (missing = ignored); cycles are detected with a warning.
4. Reload: default shortcuts Linux `ctrl+shift+,`, macOS `cmd+shift+,`; some options cannot be reloaded dynamically or only apply to new terminals. Never reload the user's session without consent.

## Standalone Config Checklist

- [ ] Preferred: a self-contained new file — all colors written out, no `theme =`, no `config-file`
- [ ] When preserving original behavior: copy the original main config and every `config-file` dependency into the output directory (default `~/.config/mood_terminal`), switch to absolute paths; override or remove color options inside the dependencies, keep non-color options
- [ ] Forbidden: copying the parent config and appending colors at its end (nested files load after it and override it)
- [ ] Non-color options (e.g. `background-opacity`, fonts) keep their original values; never adjust them to chase a contrast number

## Validation and Launch

```bash
# See which flags this version's action supports (action flags differ from launch flags)
ghostty +validate-config --help
# Syntax validation (when this version supports it)
ghostty +validate-config --config-file=/abs/path/new.conf
# Dump all default options with docs to check key spelling
ghostty +show-config --default --docs | less
```

Some versions' actions do not support `--config-default-files` or `+show-config --config-file`; when the final effective config cannot be checked, report that honestly instead of pretending validation passed. Syntax validation, final effective values, and actual GUI rendering are three distinct levels.

Standalone launch:

```bash
# Linux
ghostty --config-default-files=false --config-file=/abs/path/new.conf
# macOS
open -na Ghostty.app --args --config-default-files=false --config-file=/abs/path/new.conf
```

Flag support per the local `ghostty --help`.
