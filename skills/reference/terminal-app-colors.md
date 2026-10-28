# Terminal.app Color Configuration (Practical Reference)

Sources: AppleScript dictionary of Terminal.app and real `.terminal` profile files (structure verified 2026-10-28). macOS only.

## Capability — read first (partial support)

- `.terminal` profile files store colors (`BackgroundColor`, `TextColor`, `CursorColor`, …) as base64 **binary NSKeyedArchiver blobs** — these cannot be reliably hand-written. Do not generate `.terminal` files for colors.
- Scriptable alternative: AppleScript can set the **base colors** of a settings set (profile): background, normal text, bold text, cursor. The 16-color **ANSI palette is not exposed to AppleScript** — those are set in the GUI (Settings → Profiles → profile → Colors).

## Config Discovery and Precedence

- List profiles: `osascript -e 'tell application "Terminal" to get name of every settings set'`.
- All settings live in `~/Library/Preferences/com.apple.Terminal.plist`; per-profile colors are edited in Settings → Profiles. Discovery is read-only — never read or write that plist wholesale with `defaults`.

## Apply (requires explicit consent — this modifies user settings)

1. Ask the user to duplicate a profile first (Settings → Profiles → ⋯ → Duplicate) or name one to change (see discovery above).

2. RGB components are 0–65535; conversion is exact: `value = hex_channel × 257`. Example `#202838` → `{8224, 10280, 14392}`; `#d8dee9` → `{55512, 57054, 59881}`.

```bash
osascript -e 'tell application "Terminal"
  set background color of settings set "PROFILE" to {8224, 10280, 14392}
  set normal text color of settings set "PROFILE" to {55512, 57054, 59881}
  set cursor color of settings set "PROFILE" to {55512, 57054, 59881}
end tell'
```

Which properties are settable can vary by macOS version — test on one property first and report failures honestly. Changes apply to new windows/tabs.

## Standalone Generation Checklist

- [ ] Never write to Terminal settings without explicit consent
- [ ] Give the user the exact `osascript` commands (generate them in the report or as a file in the output directory for review)
- [ ] List the 16 ANSI hex values in the report for manual entry in Settings → Profiles → Colors

## Validation and Launch

No validator; open a new window on the changed profile and check the base colors. The ANSI palette cannot be verified programmatically — state that it was set manually or not at all.
