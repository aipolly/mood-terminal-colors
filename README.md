# Mood Terminal Colors

[中文说明](README.zh-CN.md)

A skill for AI coding assistants: design coordinated, readable terminal color schemes for **Ghostty** and **Kitty** based on the user's mood today.

## Features

- Accepts any natural-language mood description, including mixed emotions; asks first when none is given.
- Designs one palette around personal preference and generates consistent foreground, background, cursor, selection, and 16-color ANSI settings for both terminals.
- Inspects existing configs and themes, writes dated configs for the requested terminals into `~/.config/mood_terminal`, adds a shell launch-function file with the exact `source` command, preserves non-color settings, and never modifies the user's defaults.
- Validates configs and text contrast as far as the environment allows, and states limitations such as transparency, terminal versions, or the absence of a GUI preview.

This repository provides an **instruction file** for AI assistants that support Skills — it is not a terminal plugin and runs nothing automatically; on each use the assistant reads the environment, designs the palette, and generates the results.

## Install

The repository root is documentation; the skill itself lives in [`skills/`](skills/) (`SKILL.md` + `reference/`). Link or copy it into the assistant's Skills directory, for example:

```bash
git clone <repo-url> ~/mood-terminal-colors
ln -s ~/mood-terminal-colors/skills ~/.agents/skills/mood-terminal-colors
```

The exact Skills directory and loading mechanism depend on the assistant in use; back up an existing target path instead of overwriting it. You may need to reload Skills or restart the assistant after installing.

## Usage

Ask in an assistant that has this skill loaded, for example:

> I'm feeling excited today — please design Ghostty and Kitty colors for my mood and generate the configs in `~/.config/mood_terminal`.

The assistant inspects the environment, generates dated configs and a launch file, and tells you which file to `source` (then `mood-kitty` / `mood-ghostty` start the recolored terminals). Always preview in a real terminal: visual feel, background transparency, and theme effects vary by device and personal taste.

## Design Principles

Color–emotion associations are shaped by culture, personal experience, and context; they are not a diagnosis and do not guarantee changing anyone's mood. Design prioritizes personal preference, harmony, and readability.

## References

The skill's internal references live in [`skills/reference/`](skills/reference/): [Kitty colors](skills/reference/kitty-colors.md), [Ghostty colors](skills/reference/ghostty-colors.md), [Mood & color](skills/reference/mood-and-color.md).

External sources:

- [APA: color and emotion](https://www.apa.org/news/podcasts/speaking-of-psychology/color-emotion)
- [Ghostty themes](https://ghostty.org/docs/features/theme) and [config reference](https://ghostty.org/docs/config/reference)
- [Kitty config reference](https://sw.kovidgoyal.net/kitty/conf/)
- [WCAG text contrast](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html)
