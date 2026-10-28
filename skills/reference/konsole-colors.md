# Konsole Color Configuration (Practical Reference)

Sources: Konsole color-scheme file format (verified against `Breeze.colorscheme` from the KDE repo, 2026-10-28) and [KDE user docs](https://docs.kde.org/stable/en/konsole/konsole/). Linux/KDE only.

## Capability

Full palette: `[Background]`, `[Foreground]` (+ `Intense` variants), `[Color0]`–`[Color7]` (ANSI 0–7) and `[Color0Intense]`–`[Color7Intense]` (ANSI 8–15). Colors are **decimal `r,g,b` triplets**, not hex. Optional `Faint` variants exist; copy the base color when unused. Schemes are selected per profile.

## Full Color Template

`konsole-mood-YYYY-MM-DD.colorscheme` (values = shared palette converted to decimal):

```ini
[General]
Description=Mood 2026-10-28

[Background]
Color=32,40,56

[BackgroundIntense]
Color=89,101,121

[Foreground]
Color=216,222,233

[ForegroundIntense]
Color=255,255,255

[Color0]
Color=32,40,56

[Color0Intense]
Color=89,101,121

[Color1]
Color=224,108,117

[Color1Intense]
Color=255,123,134

[Color2]
Color=152,195,121

[Color2Intense]
Color=181,232,144

[Color3]
Color=229,192,123

[Color3Intense]
Color=255,214,138

[Color4]
Color=97,175,239

[Color4Intense]
Color=127,196,255

[Color5]
Color=198,120,221

[Color5Intense]
Color=226,166,255

[Color6]
Color=86,182,194

[Color6Intense]
Color=139,233,240

[Color7]
Color=216,222,233

[Color7Intense]
Color=255,255,255
```

Hex → decimal: `0x20` = 32, `0x28` = 40, `0x38` = 56 (per channel, from the `#rrggbb` value).

## Config Discovery and Precedence

- User schemes: `~/.local/share/konsole/*.colorscheme`; system schemes: `/usr/share/konsole/`. Profiles are `*.profile` files with `[Appearance] ColorScheme=<name>`; a running Konsole shows its profile in Settings.
- Schemes are picked per profile; copying a scheme never changes the user's current profile until it is selected.

## Standalone Generation Checklist

- [ ] `.colorscheme` written to the output directory first
- [ ] Installing = copying into `~/.local/share/konsole/` — additive (adds one file), but still a user directory: do it with consent and provide the exact `cp` command
- [ ] Optional: a matching `*.profile` (with `[Appearance] ColorScheme=Mood YYYY-MM-DD`) so it can be launched directly

## Validation and Launch

```bash
cp /abs/path/konsole-mood-YYYY-MM-DD.colorscheme ~/.local/share/konsole/
konsole --profile "Mood" 2>/dev/null || konsole   # with the optional profile
```

No CLI validator. After copying, the scheme appears under Settings → Edit Current Profile → Appearance (or the new profile in the Profiles sidebar); open a window and check. GUI preview requires a session — otherwise report "not performed".
