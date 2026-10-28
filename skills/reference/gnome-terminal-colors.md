# GNOME Terminal Color Configuration (Practical Reference)

Sources: `gsettings`/`dconf` schema of GNOME Terminal (`org.gnome.Terminal.*`), verified 2026-10-28. Linux only.

## Capability — read first

- GNOME's default terminal since GNOME 42 is **Console (kgx)**: it does **not** support custom color palettes (it follows the system theme). If the user's terminal is kgx, say so and offer another terminal instead — do not pretend it can be themed.
- **GNOME Terminal** (the classic app, often installed separately) supports the full 16-color palette + foreground/background (+ optional cursor/highlight colors), stored **per profile in dconf — there is no config file**. Applying changes the user's settings: explicit consent required.

## Config Discovery and Precedence

`gsettings get org.gnome.Terminal.ProfilesList list` returns the profile UUIDs; each profile's color keys live under `org.gnome.Terminal.Legacy.Profile:/org/gnome/terminal/legacy/profiles:/:<uuid>/` (read with `gsettings get $KEY palette` etc.). kgx stores nothing themeable — it follows the system theme.

## Apply (requires explicit consent)

```bash
# 1. list profiles and pick one with the user
gsettings get org.gnome.Terminal.ProfilesList list
PROFILE=<uuid>
KEY=org.gnome.Terminal.Legacy.Profile:/org/gnome/terminal/legacy/profiles:/:$PROFILE/

# 2. apply the palette
gsettings set $KEY use-theme-colors false
gsettings set $KEY foreground-color '#d8dee9'
gsettings set $KEY background-color '#202838'
gsettings set $KEY palette "['#202838','#e06c75','#98c379','#e5c07b','#61afef','#c678dd','#56b6c2','#d8dee9','#596579','#ff7b86','#b5e890','#ffd68a','#7fc4ff','#e2a6ff','#8be9f0','#ffffff']"

# 3. optional cursor / selection (the *-set flags enable them)
gsettings set $KEY cursor-colors-set true
gsettings set $KEY cursor-background-color '#d8dee9'
gsettings set $KEY highlight-colors-set true
gsettings set $KEY highlight-background-color '#49566d'
```

Colors are strings (`#rrggbb`); the palette is an array of 16 strings in ANSI order. Creating a brand-new profile via dconf is possible but more invasive — prefer an existing profile chosen with the user.

## Standalone Generation Checklist

- [ ] No standalone file exists — write the gsettings commands into a snippet in the output directory (`gnome-terminal-mood-YYYY-MM-DD.sh`) for the user to review
- [ ] Never run the commands without explicit consent; they modify the user's profile
- [ ] Values verified: 16 palette entries, correct quoting, profile UUID from step 1

## Validation and Launch

`gsettings` rejects wrong types on the spot; read values back with `gsettings get $KEY palette` to confirm, then open a new GNOME Terminal window on that profile (default profile switchable in Preferences). GUI preview requires a desktop session — otherwise report "not performed".
