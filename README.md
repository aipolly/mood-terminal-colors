# Mood Terminal Colors

[中文说明](README.zh-CN.md)

A skill for AI coding assistants: it designs terminal color schemes around how you feel today. Working now for Ghostty, Kitty, WezTerm, and Alacritty; five more terminals are parked (see the table).

## Why this repo?

People dress for their mood: a crisp shirt on a confident day, an old hoodie on a slow Sunday. Terminals deserve the same. You stare at one for hours a day, yet it wears the same outfit all year.

You spent a whole evening picking colors, checking contrast, and saving that theme. A week later the terminal feels off again. The config did not change; you did. Mood shifts from day to day, and one fixed palette cannot follow.

So this skill designs a palette for today, the way you pick today's outfit. Express the mood, or use it to set a different one when a fresh feel would help. Readability stays the floor either way.

The link between color and emotion has been measured, not just assumed:

- A systematic review of 132 peer-reviewed studies (1895-2022) with 42,266 participants across 64 countries found the associations systematic and consistent, though many-to-many and shaped by hue, lightness, and saturation ([Do we feel colours?](https://doi.org/10.3758/s13423-024-02615-z)).
- A study across 30 countries with 4,598 participants found shared patterns (yellow-orange leaning joyful, blue-green leaning calm) plus real regional differences ([Jonauskaite et al., 2020](https://pubmed.ncbi.nlm.nih.gov/32900287/)).

Treat those findings as starting points. Your preference decides.

## Terminals

| Terminal | Status | How a dated config launches |
|---|---|---|
| Ghostty | available | `ghostty --config-default-files=false --config-file=<file>` |
| Kitty | available | `kitty -c <file>` |
| WezTerm | available | `wezterm start --config-file <file>` |
| Alacritty | available | `alacritty --config-file <file>` |
| iTerm2 | parked | preset import edits app settings; no config-file launch |
| Terminal.app | parked | colors only settable in user settings, ANSI palette GUI-only |
| Warp | parked | theme must be copied into `~/.warp/themes` and picked in the GUI |
| GNOME Terminal | parked | no config file; dconf writes the user's profile |
| Konsole | parked | scheme must be installed into `~/.local/share/konsole` |

The strictness is the point. A mood palette is a one-day experiment: every file lands in `~/.config/mood_terminal`, you launch it with an explicit command, and your real setup never changes. A terminal that cannot do that stays parked until a safe path shows up. The parked formats are still documented in `skills/reference/`, and ideas for safe paths are welcome as PRs.

## What it does

- Takes any mood in plain words, mixed ones included. When the words are fuzzy, it asks up to five quick questions, broad family first and the exact flavor second, then designs.
- Designs one palette (background, foreground, cursor, selection, 16 ANSI colors) and checks contrast against 4.5:1 with WCAG math.
- Writes dated configs plus a launch file to `~/.config/mood_terminal`, carries over your non-color settings, and leaves your real configs alone.
- Reports what was validated and what was not: syntax, colors, GUI preview, transparency caveats.

This repo is an instruction file for assistants that support Skills. Nothing runs on its own; on each use the assistant reads your setup, designs, and writes the files.

## Install

```bash
git clone https://github.com/aipolly/mood-terminal-colors.git ~/mood-terminal-colors
ln -s ~/mood-terminal-colors/skills ~/.agents/skills/mood-terminal-colors
```

Adjust the skills directory to your assistant if it differs. An agent installing this for a user can follow [FOR-AGENTS.md](FOR-AGENTS.md). You may need to reload skills or restart after installing.

## Usage

> I'm feeling excited today. Design Kitty colors for my mood.

The assistant may ask a couple of quick questions first (excited-bright or excited-restless? dark or light?), then writes a dated config and a launch file, and tells you the exact `source` line. After sourcing, `mood-kitty` starts the recolored terminal. Judge it in a real window: screens, transparency, and taste differ.

## Vibe coding, honestly

This project is vibe-coded: written quickly with AI for the fun of it, with no test suite behind it. It may not fit every terminal version or machine. If something breaks, or you see a better way, pull requests are welcome.

## Design principles

Color-mood links are shaped by culture, experience, and context. A palette is not a diagnosis and will not fix a bad day. Preference, harmony, and readability come first.

## References

One practical file per terminal under `skills/reference/`, plus [Mood & color](skills/reference/mood-and-color.md) for research boundaries, the five-question mood check-in, starter palettes, the contrast method, and ANSI semantics.

External sources:

- [APA: color and emotion](https://www.apa.org/news/podcasts/speaking-of-psychology/color-emotion)
- [Ghostty docs](https://ghostty.org/docs/config), [Kitty docs](https://sw.kovidgoyal.net/kitty/conf/), [WezTerm docs](https://wezterm.org/config/files.html), [Alacritty man page](https://github.com/alacritty/alacritty/blob/master/extra/man/alacritty.5.scd)
- [WCAG contrast](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html)
