# Warp Color Configuration (Practical Reference)

**Status: parked.** This terminal cannot launch a dated config that lives outside user settings, so the skill does not generate for it by default; see the README for the reasoning. Kept for when a safe path appears; ideas welcome via PR.

Source: [Warp custom themes docs](https://docs.warp.dev/terminal/appearance/custom-themes). Retrieved 2026-10-28; trust the local Warp version and docs.

## Capability

Theme = one YAML file with `background`/`foreground`/`accent`/optional `cursor`, `details` (`darker` or `lighter`), and the 16 ANSI colors as **named keys** under `terminal_colors.normal` / `.bright`. All hex values must start with `#`. No separate selection or cursor-text control. Themes are picked in the GUI — Warp detects files automatically.

## Full Color Template

`warp-mood-YYYY-MM-DD.yaml`:

```yaml
name: Mood 2026-10-28
accent: '#98c379'
background: '#202838'
foreground: '#d8dee9'
details: darker
terminal_colors:
  normal:
    black: '#202838'
    red: '#e06c75'
    green: '#98c379'
    yellow: '#e5c07b'
    blue: '#61afef'
    magenta: '#c678dd'
    cyan: '#56b6c2'
    white: '#d8dee9'
  bright:
    black: '#596579'
    red: '#ff7b86'
    green: '#b5e890'
    yellow: '#ffd68a'
    blue: '#7fc4ff'
    magenta: '#e2a6ff'
    cyan: '#8be9f0'
    white: '#ffffff'
```

The `name` (shown in the theme picker) does not have to equal the filename; `details: lighter` for light backgrounds.

## Config Discovery and Precedence

- Themes live in `~/.warp/themes/` (macOS), `%APPDATA%\warp\Warp\data\themes\` (Windows), `${XDG_DATA_HOME:-$HOME/.local/share}/warp-terminal/themes/` (Linux).
- First discovery of a new theme may take minutes or a Warp restart; after that, changes apply within seconds.

## Standalone Generation Checklist

- [ ] YAML written to the output directory first
- [ ] Copying it into the themes directory **adds a file to a user directory** — only with explicit consent; it never touches existing themes
- [ ] Provide the exact `cp` command plus the picker path: Settings → Appearance → Themes

## Validation and Launch

No CLI validator. After copying, check the theme name appears in Settings → Appearance → Themes and that selecting it recolors the terminal live; report honestly what was and was not verified.
