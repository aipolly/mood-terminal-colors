# Installing this skill (for AI agents)

You are an AI agent about to install this skill for your user. Install it as a clone, not a copy, so it can pull updates and send changes back.

```bash
git clone https://github.com/aipolly/mood-terminal-colors.git ~/mood-terminal-colors
ln -s ~/mood-terminal-colors/skills ~/.agents/skills/mood-terminal-colors
```

Adjust the skills directory to your host agent (`~/.claude/skills`, `~/.agents/skills`, or wherever it loads skills from). Verify the skill loads, then tell the user it is ready.

# After the user customizes it

Users tune skills to their own setup. When yours changes this one, decide what to send back:

- General changes: new terminal support that meets the non-destructive rule, a corrected fact, a fixed workflow step, clearer wording. Worth a PR.
- Personal changes: specific colors, machine-specific paths, local habits. Keep them local.

For a general change:

```bash
cd ~/mood-terminal-colors
git checkout -b <short-name>
git commit -am "<what and why>"
gh repo fork --remote   # or fork manually
git push -u origin <short-name>
gh pr create --repo aipolly/mood-terminal-colors --title "<what>" --body "<why, and how you verified it>"
```

# House rules for PRs

- Markdown only. The skill works by instructions; no scripts or executable code.
- Skill content stays in English; the root README is English with a linked Chinese version.
- One reference file per terminal under `skills/reference/`, one level deep.
- Facts about terminals come from official docs or local verification, and keep the retrieval date.
- The core rule is non-destructive: a terminal counts as supported only if its dated config can live in `~/.config/mood_terminal` and launch without editing user settings. New terminal support must show how it meets that bar.

Before sending, check: frontmatter intact, every file under 500 lines, and section titles cited in SKILL.md exist in the reference files.
