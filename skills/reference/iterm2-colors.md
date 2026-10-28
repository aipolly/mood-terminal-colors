# iTerm2 Color Configuration (Practical Reference)

**Status: parked.** This terminal cannot launch a dated config that lives outside user settings, so the skill does not generate for it by default; see the README for the reasoning. Kept for when a safe path appears; ideas welcome via PR.

Sources: [iTerm2 documentation](https://iterm2.com/documentation-one-page.html) and the community preset collection format ([iTerm2-Color-Schemes](https://github.com/mbadolato/iTerm2-Color-Schemes)); structure verified against a real `.itermcolors` preset 2026-10-28.

## Capability

Full palette: foreground/background, cursor + cursor text, selection background + selected text, bold, and ANSI 0–15. A color preset is an XML plist where each color is a dict of **float components 0–1** (4 decimals), not hex. Presets apply via GUI import only — there is no safe CLI import (`defaults import` would replace the entire preferences plist; never use it).

## Full Color Template

`iterm2-mood-YYYY-MM-DD.itermcolors` — one `<key>`/`<dict>` pair per color; every color dict has the same structure:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>Background Color</key>
	<dict>
		<key>Alpha Component</key><real>1</real>
		<key>Color Space</key><string>sRGB</string>
		<key>Red Component</key><real>0.1255</real>
		<key>Green Component</key><real>0.1569</real>
		<key>Blue Component</key><real>0.2196</real>
	</dict>
	<!-- same structure for: Foreground Color, Cursor Color, Cursor Text Color,
	     Selection Color, Selected Text Color, Bold Color,
	     Ansi 0 Color … Ansi 15 Color -->
</dict>
</plist>
```

Hex → component conversion: `round(channel / 255, 4)`. Example: `#202838` → R `0.1255` (0x20=32), G `0.1569` (0x28=40), B `0.2196` (0x38=56). Keep `Alpha Component` at 1 and `Color Space` at sRGB.

## Config Discovery and Precedence

- All iTerm2 settings live in `~/Library/Preferences/com.googlecode.iterm2.plist`; colors are per-profile ("Settings → Profiles → <profile> → Colors"). Ask which profile the user runs.
- Do not dump the whole plist (it is huge and may contain secrets); the GUI color panel is the honest way to inspect current colors.

## Standalone Generation Checklist

- [ ] `.itermcolors` file written to the output directory only — nothing is applied automatically
- [ ] All 16 ANSI keys plus the base keys present; every component converted from the shared hex palette

## Validation and Launch

```bash
plutil -lint /abs/path/iterm2-mood-YYYY-MM-DD.itermcolors   # macOS plist syntax check
```

Import (user does this in the GUI): iTerm2 → Settings → Profiles → <profile> → Colors → Color Presets → **Import…** → pick the file → click the preset name to apply. Applying affects that profile only; suggest trying on a duplicated profile if the user is unsure.
