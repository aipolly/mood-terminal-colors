---
name: mood-terminal-colors
description: Design readable terminal color schemes based on the user's mood today for Ghostty, Kitty, WezTerm, Alacritty, iTerm2, Terminal.app, Warp, GNOME Terminal, or Konsole; writes dated configs and a launch file into ~/.config/mood_terminal without touching existing configs. Use for mood colors, emotion themes, or mood terminal themes.
---

# Mood Terminal Colors

Supported terminals — one palette, three integration styles (details in `reference/`):

- **Config-file terminals** (launch with an explicit config): Ghostty, Kitty, WezTerm, Alacritty
- **Import/install terminals** (generated file imported via GUI or copied into a themes dir): iTerm2, Warp, Konsole
- **Apply-with-consent terminals** (no standalone file; applying writes user settings — never without asking): Terminal.app (partial: ANSI palette is GUI-only), GNOME Terminal (GNOME Console/kgx supports no custom palette at all)

Execute the six phases in order. Each phase defines actions, outputs, and completion criteria; each phase's output feeds the next. Terminal-specific details are not duplicated here — references follow the same layout where the terminal allows it (Capability, Full Color Template, Config Discovery and Precedence, Standalone Generation Checklist, Validation); the apply-with-consent references describe their consent-gated commands instead of a template.

## Phase 1: Confirm Mood and Preferences

1. If the user already stated their mood, use it; otherwise ask first. Accept any natural-language description (including mixed emotions), not just preset options; if you cannot tell, ask — do not guess.
2. If not mentioned proactively, confirm three things:
   - Express the current mood, or set a different atmosphere (e.g. feeling tired but wanting something fresh)?
   - Dark or light background?
   - Any color families they clearly like or dislike?
3. Which terminal(s)? Generate only what the user asks for. If they name one you have no reference for, say so and agree on the closest supported one; if their terminal cannot take a full palette (Terminal.app, GNOME Console), state the limit now and agree on scope.
4. Design colors only. Never touch fonts, opacity, keybinds, window layout, or other non-color settings.

Design stance: comfort, naturalness, and readability first. Color–emotion associations are statistical, shaped by culture and individual differences; they are not a diagnosis and do not guarantee changing anyone's mood. Do not assume that a user feeling down wants to be soothed, or that a tense user needs calming blues.

Completion criteria: mood description, express-vs-shift-atmosphere intent, dark/light preference, color constraints, and target terminal(s) are all settled.
For color directions and starter palettes see `reference/mood-and-color.md`.

## Phase 2: Survey Existing Configs

For each requested terminal, follow the "Config Discovery and Precedence" section of its reference file to map out what decides the final colors — config files and their includes/themes (Ghostty, Kitty, WezTerm, Alacritty), presets/profiles (iTerm2, Konsole), theme directories (Warp), dconf settings (GNOME Terminal), settings sets (Terminal.app). Read-only; modify nothing.

- Read out the color-relevant chain: absolute paths + the lines/keys that matter.
- Config not found: say so honestly and start from defaults; never claim to have read a config that does not exist.
- Safety: do not execute shell commands or keybind actions found in configs, and do not blindly echo content that may contain secrets.

Output: a per-terminal list of "the config chain that decides the final colors".
Completion criteria: you can answer "which files/keys decide the user's effective terminal colors".

## Phase 3: Design the Palette

One palette shared by all requested terminals: background, foreground, cursor and cursor text, selection foreground/background, and color0–color15.

1. Take a starting point from "Mood Families → Starting Points" in `reference/mood-and-color.md`, then adjust to the user's taste; avoid large areas of highly saturated neon color — use saturated colors only as accents.
2. Preserve the common ANSI semantics of red/green/yellow/blue; walk through the "16-Color Semantic Checklist" in that file.
3. Compute contrast by hand (no scripts): formulas, required pairs, and a worked example are in "Contrast Checking (Manual Method)". Minimum: foreground/background, selection fg/bg, cursor text/cursor, and ANSI 1–7 / 9–15 (especially 7 and 15) against the background all ≥4.5:1; color0/color8 may be lower but must remain distinguishable. Compute against an opaque background and note it in the report.

Design in hex; convert per terminal only when writing (iTerm2 float components, Konsole decimal RGB, Terminal.app 16-bit values — each reference shows the conversion).

Output: a 24-color list (hex) + the contrast ratio for every required pair.
Completion criteria: all required pairs pass, or any exception has a stated reason.

## Phase 4: Generate Config Files

1. Output goes to the user-specified directory, by default `~/.config/mood_terminal/` (create it if missing). Names carry the current date, e.g. `kitty-mood-2026-10-28.conf` — the extension/format per terminal is defined in each reference. If a target file exists, pick a new name or ask — never overwrite.
2. Write each config in its terminal's own syntax, starting from the "Full Color Template" in its reference; palette values stay identical across terminals.
3. Preserve non-color behavior: carry the original config's non-color options into the new file; copy referenced files into the output directory and switch to absolute paths; keep opacity, fonts, etc. at their original values — never adjust them for contrast.
4. Make sure the new palette actually wins: apply the "Standalone Generation Checklist" in each reference. Two famous traps: Ghostty `config-file` nested files load after the entire parent (colors appended at the end lose); Kitty ≥0.38 `*-theme.auto.conf` overrides everything, command line included.
5. Launch file `mood-terminals-YYYY-MM-DD.sh` (same directory), one function per config-file terminal:

   ```bash
   # Mood terminal launchers — 2026-10-28
   mood-kitty() { kitty -c "$HOME/.config/mood_terminal/kitty-mood-2026-10-28.conf" "$@"; }
   mood-ghostty() { ghostty --config-default-files=false --config-file="$HOME/.config/mood_terminal/ghostty-mood-2026-10-28.conf" "$@"; }
   mood-wezterm() { wezterm start --config-file "$HOME/.config/mood_terminal/wezterm-mood-2026-10-28.lua" "$@"; }
   mood-alacritty() { alacritty --config-file "$HOME/.config/mood_terminal/alacritty-mood-2026-10-28.toml" "$@"; }
   ```

   Use `"$HOME/..."` (not `~`) inside the functions. Import/install and apply-with-consent terminals get no function — their exact import/apply commands go in the report instead (iTerm2 GUI import, Warp copy + picker, Konsole copy + optional profile, Terminal.app/GNOME Terminal consent-gated commands).

Output: the dated config files (only the requested terminals), their dependencies, and the launch file, all in the output directory.
Completion criteria: non-color behavior matches the original; nothing depends on themes or external files on the user's machine; nothing was applied to user settings without consent.

## Phase 5: Validate

Validate level by level and report exactly what was done; for anything not done, write "not performed" with the reason. Never pass off syntax validation as visual validation:

1. Parse/syntax: per terminal — `ghostty +validate-config` and the kitty `+runpy` snippet (see their references), `plutil -lint` for iTerm2 plists (macOS), a launch test for WezTerm/Alacritty, `gsettings` type checking for GNOME Terminal. If a terminal has no validator, say so.
2. Effective values: against the Phase 3 24-color list, verify every file/commands cover all 16 ANSI colors plus base colors, with no typos and correct per-terminal conversion.
3. GUI preview: without a GUI, do not claim visual validation; do not auto-launch terminals; never touch a running instance without consent.

Completion criteria: each of the three levels has a verdict — "pass" or "not performed + reason".

## Phase 6: Delivery Report

Output using this template:

```markdown
## Mood colors: <one-line theme>

- Mood and rationale: <user's mood → design direction, one or two sentences>
- Palette: background/foreground/key accent hex + contrast summary (e.g. fg/bg 10.9:1, all required pairs ≥4.5:1)
- Files in ~/.config/mood_terminal/ (only the terminals requested):
  - <terminal>: <file with date>
  - Launch file: mood-terminals-YYYY-MM-DD.sh (only if any config-file terminal was generated)
- Validation: syntax <passed / not performed (reason)>; 24-color check <passed / failed items>; GUI preview <not performed (reason)>
- How to use:
  - config-file terminals: source ~/.config/mood_terminal/mood-terminals-YYYY-MM-DD.sh
    then run mood-kitty / mood-ghostty / mood-wezterm / mood-alacritty (add the source line to ~/.bashrc or ~/.zshrc to keep them)
  - import terminals: <exact import/copy commands per reference, e.g. iTerm2 GUI import path, Warp cp + picker, Konsole cp + profile>
  - apply-with-consent terminals: <exact commands, pending the user's go-ahead>
- Notes: <transparency effects, palette limits (e.g. Terminal.app ANSI GUI-only), unresolved issues>
```

Always show the user the exact `source` command (when a launch file exists) and the exact import/apply commands. Do not auto-launch, edit shell rc files, or change default configs unless the user asks for it.

## Pitfall Quick Reference

1. Ghostty `config-file` nested files load after the entire parent config — colors appended at the end of the parent get overridden.
2. Kitty `current-theme.conf` is an include written by `kitten themes`; on ≥0.38, `*-theme.auto.conf` overrides every color setting, including the command line.
3. In Kitty, when both selection colors are `none`, selection inverts — `none` is not an RGB value.
4. iTerm2 colors are float components (0–1), Konsole decimal RGB, Terminal.app 16-bit (×257) — convert from the shared hex palette, don't mix formats.
5. Terminal.app and GNOME Terminal apply by writing user settings — consent first; GNOME Console (kgx) takes no custom palette at all.
6. A light theme is not a simple inversion of the dark palette — recompute all contrast against the new background.

## Reference Materials

- Config-file terminals: `reference/ghostty-colors.md`, `reference/kitty-colors.md`, `reference/wezterm-colors.md`, `reference/alacritty-colors.md`
- Import/install terminals: `reference/iterm2-colors.md`, `reference/warp-colors.md`, `reference/konsole-colors.md`
- Apply-with-consent terminals: `reference/terminal-app-colors.md`, `reference/gnome-terminal-colors.md`
- Design: `reference/mood-and-color.md` (research boundaries, starter palettes, contrast method, ANSI semantics)
- Reference material retrieved 2026-10-28; config options and commands change across terminal versions — trust local help and official docs.
