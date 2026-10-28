# AGENTS.md

Conventions for AI agents working in this repository. The user's standing requirements are recorded here so they persist across sessions.

## Repository purpose

This is a **skill repo**: the skill itself lives in `skills/` (`SKILL.md` + `reference/*.md`). The repo root holds documentation only (`README.md`, `README.zh-CN.md`, this file, `.gitignore`). Work on the skill in place — never install, copy, or symlink it into global agent directories (e.g. `~/.agents/skills/`) as part of editing it.

## Hard rules

1. **No scripts.** Do not add Python, shell, or any executable code. The skill relies purely on LLM abilities reading markdown instructions. One-off checks run as ad-hoc shell commands and are not committed.
2. **All skill content in English.** `skills/SKILL.md` and everything under `skills/reference/` is written in English.
3. **README language.** `README.md` is the English default and links to `README.zh-CN.md` at the top; the Chinese version links back to English.
4. **Layout.** Skill content stays under `skills/`; the root stays documentation-only. `reference/` is one level deep with `.md` files only; no README or index file inside the skill directory.
5. **Verified facts only.** Terminal commands and config rules written into the skill must come from official docs or local verification; keep the retrieval date and the "trust local help/version" hedges.

## Change workflow

After editing skill files:

1. **Check**: frontmatter present; every file < 500 lines; no stray files under `skills/`; section titles cited in `SKILL.md` exist in the reference files; README language links intact.
2. **Commit** with a concise imperative message.
3. **Push** to `origin/main` (public repo: https://github.com/aipolly/mood-terminal-colors).
