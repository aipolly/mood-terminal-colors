---
name: mood-terminal-colors
description: Design harmonious, readable terminal color schemes for Ghostty and Kitty based on the user's mood today, and generate standalone config files in the working directory. Use when the user mentions mood colors, emotion themes, mood-based Ghostty or Kitty colors, or mood terminal theme. Follows a six-phase workflow - confirm mood and preferences, survey existing configs, design the palette and check contrast, generate two standalone configs, validate, deliver a report. Never modifies the user's original configs or non-color settings; requires no external scripts.
---

# Mood Terminal Colors (Ghostty + Kitty)

Execute the six phases in order. Each phase defines actions, outputs, and completion criteria; each phase's output feeds the next. Details are not duplicated here — consult the matching section in `reference/` as noted per phase.

## Phase 1: Confirm Mood and Preferences

1. If the user already stated their mood, use it; otherwise ask first. Accept any natural-language description (including mixed emotions), not just preset options; if you cannot tell, ask — do not guess.
2. If not mentioned proactively, confirm three things:
   - Express the current mood, or set a different atmosphere (e.g. feeling tired but wanting something fresh)?
   - Dark or light background?
   - Any color families they clearly like or dislike?
3. Design colors only. Never touch fonts, opacity, keybinds, window layout, or other non-color settings.

Design stance: comfort, naturalness, and readability first. Color–emotion associations are statistical, shaped by culture and individual differences; they are not a diagnosis and do not guarantee changing anyone's mood. Do not assume that a user feeling down wants to be soothed, or that a tense user needs calming blues.

Completion criteria: mood description, express-vs-shift-atmosphere intent, dark/light preference, and color constraints are all settled.
For color directions and starter palettes see `reference/mood-and-color.md`.

## Phase 2: Survey Existing Configs

Map out the complete chain of configs that decide the final colors for both terminals. Read-only; modify nothing.

- Kitty: record the version with `kitty --version`. Config locations in order: `KITTY_CONFIG_DIRECTORY`, `$XDG_CONFIG_HOME/kitty/kitty.conf`, `~/.config/kitty/kitty.conf`. Read the main config and every `include` dependency (relative paths resolve against the including file); check whether `current-theme.conf` is included; on kitty ≥0.38 check `dark-theme.auto.conf` / `light-theme.auto.conf` / `no-preference-theme.auto.conf` — when present, the chosen auto theme overrides every other color setting, including command-line overrides.
- Ghostty: record the version with `ghostty +version`. Config is `$XDG_CONFIG_HOME/ghostty/config.ghostty` (older versions: `config`; without XDG set, usually `~/.config/ghostty/`); macOS additionally loads from `~/Library/Application Support/com.mitchellh.ghostty/`. Multiple files load in the documented order; later files override earlier ones. Read out `theme =` lines and the `config-file` nesting chain (nested files load after the entire parent config and may nest further).
- Config not found: say so honestly and start from built-in defaults; never claim to have read a config that does not exist.
- Safety: do not execute shell commands or keybind actions found in configs (kitty `launch`/`map`, Ghostty `command`, etc.), and do not blindly echo content that may contain secrets.

Output: a per-terminal list of "the config chain that decides the final colors" (absolute file paths + relevant line numbers and content).
Completion criteria: you can answer "which files and lines decide the user's effective terminal colors".
See the "Config Discovery and Load Precedence" sections of `reference/kitty-colors.md` and `reference/ghostty-colors.md`.

## Phase 3: Design the Palette

One palette shared by both terminals: background, foreground, cursor and cursor text, selection foreground/background, and color0–color15.

1. Starting from the mood and preferences from Phase 1, take a starting point from "Mood Families → Starting Points" in `reference/mood-and-color.md`, then adjust to the user's taste; avoid large areas of highly saturated neon color — use saturated colors only as accents.
2. Preserve the common ANSI semantics of red/green/yellow/blue (error/success/warning/links and directories); walk through the "16-Color Semantic Checklist" in that file.
3. Compute contrast by hand (no scripts): formulas, required pairs, and a worked example are in "Contrast Checking (Manual Method)" in `reference/mood-and-color.md`. Minimum requirements: foreground/background, selection foreground/background, cursor text/cursor, and the ANSI colors commonly used as foreground (1–7, 9–15, especially 7 and 15) against the background all ≥4.5:1; color0/color8 may be lower but must remain distinguishable. Compute against an opaque background; if the user has transparency enabled, note in the report that actual appearance will differ.

Output: a 24-color list (hex) + the contrast ratio for every required pair.
Completion criteria: all required pairs pass, or any exception has a stated reason (e.g. color0 close to background).

## Phase 4: Generate Config Files

1. Write into the user-specified or current working directory; if a target file already exists, pick a new name or ask — never overwrite.
2. Syntax mapping (full templates in the "Full Color Template" sections of both references):

   | Meaning | Ghostty | Kitty |
   |---|---|---|
   | Foreground / background | `foreground` / `background` | same names |
   | Cursor / cursor text | `cursor-color` / `cursor-text` | `cursor` / `cursor_text_color` |
   | Selection fg / bg | `selection-foreground` / `selection-background` | `selection_foreground` / `selection_background` |
   | ANSI N | `palette = N=#RRGGBB` | `colorN #RRGGBB` |

3. Preserve non-color behavior: carry the original config's non-color options into the new file; copy any files the original config references into the working directory and switch to absolute paths; keep opacity, fonts, etc. at their original values — never adjust them for contrast.
4. Make sure the new palette actually wins (common override traps):
   - Ghostty: prefer a self-contained new file (no `theme =`, no `config-file`); if you must keep the original nesting, copy all dependencies into the working directory and handle the color options inside them. Never copy the parent config and append colors at its end — nested files load after the entire parent and will override them.
   - Kitty: the new file must not include `current-theme.conf` or any `*-theme.auto.conf`; when copying the original config chain, remove or rewrite its theme includes.
   - Check title bar, tab bar, window borders, `url_color`, and other color-touching options (option list in `reference/kitty-colors.md`).

Output: two new configs plus all their dependencies, in the same working directory, referencing each other by absolute path.
Completion criteria: non-color behavior matches the original; neither file depends on themes or external files on the user's machine.
See the "Standalone Config Checklist" sections of both references.

## Phase 5: Validate

Validate level by level and report exactly what was done; for anything not done, write "not performed" with the reason. Never pass off syntax validation as visual validation:

1. Parse/syntax:
   - Ghostty: first run `ghostty +validate-config --help` to see which flags this version supports, then `ghostty +validate-config --config-file=<absolute path>`; action flags differ from normal launch flags — if it cannot run, report that honestly.
   - Kitty: use `kitty +runpy` calling `kitty.config.load_config` for a parse check (snippet in the "Validation" section of `reference/kitty-colors.md`); the internal API may change between versions — report failures honestly.
2. Effective values: against the Phase 3 24-color list, verify both files contain all 16 ANSI colors plus the base colors, with no typos.
3. GUI preview: without a GUI, do not claim visual validation; do not auto-launch terminals; never touch a running instance via `kitten @ set-colors` without consent.

Completion criteria: each of the three levels has a verdict — "pass" or "not performed + reason".

## Phase 6: Delivery Report

Output using this template:

```markdown
## Mood colors: <one-line theme>

- Mood and rationale: <user's mood → design direction, one or two sentences>
- Palette: background/foreground/key accent hex + contrast summary (e.g. fg/bg 10.9:1, all required pairs ≥4.5:1)
- Files (absolute paths):
  - Kitty: <path>
  - Ghostty: <path>
- Validation: syntax <passed / not performed (reason)>; 24-color check <passed / failed items>; GUI preview <not performed (reason)>
- Launch commands:
  - kitty -c <absolute path>
  - ghostty --config-default-files=false --config-file=<absolute path>
    (macOS: open -na Ghostty.app --args --config-default-files=false --config-file=<absolute path>)
- Notes: <transparency effects, unresolved issues, etc.>
```

Launch flags depend on the local version (`kitty -c` merges onto built-in defaults and does not load the user config). Do not auto-launch or change default configs unless the user asks for it.

## Pitfall Quick Reference

1. Ghostty `config-file` nested files load after the entire parent config — colors appended at the end of the parent get overridden by nested files.
2. Kitty `current-theme.conf` is an include written by `kitten themes`; on ≥0.38, `*-theme.auto.conf` overrides every color setting, including the command line.
3. In Kitty, when both selection colors are `none`, selection inverts — `none` is not an RGB value.
4. A light theme is not a simple inversion of the dark palette — recompute all contrast against the new background.
5. Both terminals can share one set of color values, but their syntax and precedence rules differ — validate each file separately.

## Reference Materials

- `reference/kitty-colors.md`: full color template, config discovery and load precedence, validation snippet, standalone launch.
- `reference/ghostty-colors.md`: full color template, load order and nesting rules, validation, standalone launch.
- `reference/mood-and-color.md`: research boundaries, mood-family starting points, manual contrast method, 16-color semantic checklist.
- Reference material retrieved 2026-10-28; config options and commands change across terminal versions — trust local help and official docs.
