# Kitty Color Configuration (Practical Reference)

Sources: [kitty.conf reference](https://sw.kovidgoyal.net/kitty/conf/), [invocation](https://sw.kovidgoyal.net/kitty/invocation/), [themes kitten](https://sw.kovidgoyal.net/kitty/kittens/themes/), [remote control](https://sw.kovidgoyal.net/kitty/remote-control/). Retrieved 2026-10-28; trust the local `kitty --version` and each command's `--help`.

## Full Color Template

One `option value` per line; standalone comment lines start with `#`. The sample values work as a workflow starting point:

```conf
# Base
foreground           #d8dee9
background           #202838
# Cursor
cursor               #d8dee9
cursor_text_color    #202838
# Selection (both set to none = inverted behavior; none is not an RGB value)
selection_foreground #ffffff
selection_background #49566d
# ANSI 0-7: black red green yellow blue magenta cyan white
color0  #202838
color1  #e06c75
color2  #98c379
color3  #e5c07b
color4  #61afef
color5  #c678dd
color6  #56b6c2
color7  #d8dee9
# ANSI 8-15: bright variants
color8  #596579
color9  #ff7b86
color10 #b5e890
color11 #ffd68a
color12 #7fc4ff
color13 #e2a6ff
color14 #8be9f0
color15 #ffffff
# Optional UI colors (write only the ones the original config actually uses)
active_tab_foreground   #202838
active_tab_background   #98c379
inactive_tab_foreground #d8dee9
inactive_tab_background #3b4252
active_border_color     #98c379
inactive_border_color   #3b4252
url_color               #61afef
```

## Option Quick Reference

| Meaning | Option |
|---|---|
| Text / background | `foreground`, `background` |
| Cursor / cursor text | `cursor`, `cursor_text_color` |
| Selection text / background | `selection_foreground`, `selection_background` |
| ANSI palette | `color0` … `color15` (higher indexes also configurable) |
| Tabs | `active_tab_foreground/background`, `inactive_tab_foreground/background` |
| Window borders | `active_border_color`, `inactive_border_color`, `bell_border_color` |
| Links / marks | `url_color`, `mark1_foreground/background`, etc. |

Programs in the terminal may also emit true-color RGB directly, bypassing the 16-color table; the palette only affects programs that pick colors by index.

## Config Discovery and Precedence

1. Version and location: `kitty --version`. Without `-c`, kitty takes the first existing file among `$XDG_CONFIG_HOME/kitty/kitty.conf` → `~/.config/kitty/kitty.conf` → `$XDG_CONFIG_DIRS/kitty/kitty.conf`; `KITTY_CONFIG_DIRECTORY` overrides this.
2. `kitty -c <file>`: the given files are **merged onto the built-in defaults** (not a full replacement; use the special value `NONE` to load nothing). `-c` may be given multiple times; files merge in order, later overriding earlier.
3. `include path`: relative paths resolve against the **including file**; load order is precedence — later loads override earlier ones.
4. Theme machinery, three facts:
   - `kitten themes` selects and previews themes interactively, but **writes to** the user config and `current-theme.conf` — forbidden in this task.
   - Read-only inspection of a theme: `kitten themes --dump-theme 'name'` (behavior per local help).
   - kitty ≥0.38: when `dark-theme.auto.conf` / `light-theme.auto.conf` / `no-preference-theme.auto.conf` exists, the chosen auto theme overrides every other color setting, **including command-line overrides**.
5. Recoloring a running instance requires remote control `kitten @ set-colors`, gated by the kitty.conf permission settings; do not use it without consent, and never enable or widen remote control just for recoloring.

## Standalone Config Checklist

- [ ] The new file does not include `current-theme.conf` or any `*-theme.auto.conf`
- [ ] Non-color options from the original config are carried over (`font_size`, `background_opacity`, etc. keep their original values)
- [ ] Original include dependencies are copied into the output directory (default `~/.config/mood_terminal`) and referenced by absolute path; color options inside them handled, non-color options kept
- [ ] The filename does not collide with existing files in the output directory

## Validation

Parse check (the internal API may change across versions; check the signature first):

```bash
# 1. Confirm the local load_config signature
kitty +runpy "from kitty.config import load_config; help(load_config)"
# 2. Parse check: bad lines raise an exception and a nonzero exit code; prints "parse OK" when clean
kitty +runpy "from kitty.config import load_config; load_config('/abs/path/new.conf'); print('parse OK')"
```

Then manually verify the effective values: background, foreground, cursor, selection, and all 16 indexes against the design list — parse success, final effective values, and actual GUI rendering are three distinct validation levels; report honestly about any level you did not perform. Contrast computed under transparency does not represent actual appearance.

Standalone launch: `kitty -c /abs/path/new.conf` (merged onto built-in defaults; the user config is not loaded; multiple `-c` flags merge in order).
