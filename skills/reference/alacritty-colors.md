# Alacritty Color Configuration (Practical Reference)

Sources: [alacritty.5 man page](https://github.com/alacritty/alacritty/blob/master/extra/man/alacritty.5.scd) and [official docs](https://alacritty.org/config-alacritty.html). Retrieved 2026-10-28; trust the local `alacritty --version`.

## Capability

Full palette: primary foreground/background, cursor text/cursor, selection text/background, `normal` 8 colors (ANSI 0–7), `bright` 8 colors (8–15), and optional `indexed` extras. TOML config; live reload is on by default (`live_config_reload`).

## Full Color Template

Self-contained `alacritty-mood-YYYY-MM-DD.toml`:

```toml
[colors.primary]
foreground = "#d8dee9"
background = "#202838"

[colors.cursor]
text = "#202838"
cursor = "#d8dee9"

[colors.selection]
text = "#ffffff"
background = "#49566d"

[colors.normal]
black = "#202838"
red = "#e06c75"
green = "#98c379"
yellow = "#e5c07b"
blue = "#61afef"
magenta = "#c678dd"
cyan = "#56b6c2"
white = "#d8dee9"

[colors.bright]
black = "#596579"
red = "#ff7b86"
green = "#b5e890"
yellow = "#ffd68a"
blue = "#7fc4ff"
magenta = "#e2a6ff"
cyan = "#8be9f0"
white = "#ffffff"
```

Optional extension, separate from the complete 22-role template above. Add it only when an extra indexed color is needed, substituting a valid six-digit hex color:

```toml
[[colors.indexed]]
index = 16
color = "#e5c07b"
```

## Config Discovery and Precedence

- Default config: `$XDG_CONFIG_HOME/alacritty/alacritty.toml` (usually `~/.config/alacritty/alacritty.toml`).
- Configs may `import = [ "...", ]` other TOML files; later files override earlier. Read the import chain before assuming the effective colors.
- The user's config is only read for surveying; generated files never touch it.

## Standalone Config Checklist

- [ ] Self-contained TOML in the output directory; palette identical to the shared design
- [ ] Non-color options from the user's config (font, opacity, window settings) carried over unchanged
- [ ] No `import` of user files; launched via `--config-file`

## Validation and Launch

```bash
alacritty --config-file /abs/path/alacritty-mood-YYYY-MM-DD.toml
```

A config error makes Alacritty print an error and fail to start (or show it in stderr) — a successful start with the new colors visible is the practical validation. Report honestly: launching without a GUI is "not performed".
