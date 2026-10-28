# WezTerm Color Configuration (Practical Reference)

Sources: [WezTerm docs](https://wezterm.org/config/files.html), [colors table](https://wezterm.org/config/lua/config/colors.html), [appearance](https://wezterm.org/config/appearance.html). Retrieved 2026-10-28; trust the local `wezterm --version` and docs.

## Capability

Full palette: foreground/background, cursor (`cursor_bg`, `cursor_fg`, `cursor_border`), selection (`selection_fg`, `selection_bg`), `ansi` (0–7), `brights` (8–15), plus extra `indexed` slots. Lua config; WezTerm auto-reloads the config on save, and a config error shows a warning banner in the window.

## Full Color Template

Self-contained `wezterm-mood-YYYY-MM-DD.lua`; `ansi` = colors 0–7, `brights` = 8–15:

```lua
local wezterm = require 'wezterm'
return {
  colors = {
    foreground    = '#d8dee9',
    background    = '#202838',
    cursor_bg     = '#d8dee9',
    cursor_fg     = '#202838',
    cursor_border = '#d8dee9',
    selection_fg  = '#ffffff',
    selection_bg  = '#49566d',
    ansi = { '#202838', '#e06c75', '#98c379', '#e5c07b',
             '#61afef', '#c678dd', '#56b6c2', '#d8dee9' },
    brights = { '#596579', '#ff7b86', '#b5e890', '#ffd68a',
                '#7fc4ff', '#e2a6ff', '#8be9f0', '#ffffff' },
  },
}
```

## Config Discovery and Precedence

- Default config: `~/.config/wezterm/wezterm.lua` (macOS also `~/.wezterm.lua`); `$WEZTERM_CONFIG_FILE` points to the active one — check it first.
- A `wezterm.lua` may return a function of `wezterm` for dynamic config or require other Lua files; read the chain before assuming what applies.
- The user's config is only read for surveying; generated files never touch it.

## Standalone Config Checklist

- [ ] Self-contained Lua file in the output directory; palette identical to the shared design
- [ ] Non-color options from the user's config (font, opacity, key bindings) carried over unchanged
- [ ] Launched via `--config-file`, so the user's own config stays untouched

## Validation and Launch

```bash
wezterm start --config-file /abs/path/wezterm-mood-YYYY-MM-DD.lua
```

(Check `wezterm start --help` for flag placement on the local version.) There is no separate CLI config validator: a bad config prevents startup or shows a warning banner in the window — report what you actually observed; do not claim validation without launching.
