# Mood & Color: Design and Readability in Practice

## Evidence and Boundaries (read before designing)

- Systematic review ([Do we feel colours?](https://doi.org/10.3758/s13423-024-02615-z); 132 studies, 42,266 participants): color–emotion associations are **many-to-many**; hue, lightness, and saturation all matter; the English-only literature may bias the findings.
- Cross-national study ([Jonauskaite et al., 2020](https://pubmed.ncbi.nlm.nih.gov/32900287/); 30 countries, 4,598 participants): commonalities exist, but associations vary with language and region.
- Therefore: association ≠ causation ≠ prediction for an individual. Color is not a psychological diagnosis, and do not assume the user wants colors to change their mood; user preference always outranks the tables below.

## Mood Families → Starting Points

First clarify whether the user wants to **express** their current mood or **shift** to a different atmosphere, then take a starting point below. The sample hex values are starting points, not prescriptions:

| Mood description | Design direction | Starter example (background / foreground / accent) |
|---|---|---|
| Calm, relaxed, settled | Low-saturation blue-green-cyan, soft transitions | `#1f2b2e` / `#d6e4e4` / teal `#8fd0c9` |
| Joyful, excited, energetic | Neutral background; warm accents (apricot, coral, soft yellow) | `#2b2622` / `#ece2d4` / coral `#f0a583` |
| Warm, wanting comfort | Warm neutrals (milk-tea, warm grays); gentle overall but contrast-compliant | `#2d2a26` / `#e6ddd2` / warm yellow `#e8c97a` |
| Focused, steady | Deep blue-gray base, restrained mid-lightness cool accents | `#1e2430` / `#d5dce8` / steel blue `#7ca6c8` |
| Tired, tense, low | Shift: soft blue-greens and misty purple lower stimulation; express: design from their description — do not assume they need soothing | `#262a33` / `#d8dbe4` / misty purple `#a99cc4` |
| Mixed emotions | Neutral background + two or three low-saturation accents echoing each part | combine as needed |

Rough trends from the literature (for context, not mapping rules): reds are higher-arousal and can be positive or negative; yellow-orange often pairs with joy; blue-green often pairs with relaxation; brighter colors are more often reported positive and darker ones more often negative — a group-level statistic, not "dark theme = bad mood".

## Contrast Checking (Manual Method)

Use [WCAG 2.2 AA normal text 4.5:1](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html) as the design target (borrowed; not an accessibility certification of the whole terminal):

1. Per channel, c/255 gives c′ (0–1);
2. Linearize: if c′ ≤ 0.03928 use `c′/12.92`, else `((c′+0.055)/1.055)^2.4`;
3. Relative luminance L = 0.2126R + 0.7152G + 0.0722B (with linearized channel values);
4. Contrast = (L_lighter + 0.05) / (L_darker + 0.05).

Worked example: foreground `#d8dee9`, background `#202838` → L ≈ 0.727 and 0.021 → contrast ≈ 10.9:1, passes.

Required pairs (all ≥4.5:1, except color0/8):

- foreground / background
- selection_foreground / selection_background
- cursor text / cursor (e.g. cursor_text_color / cursor)
- color1–color7 and color9–color15 against background (color7 and color15 often serve as default foregrounds — check them carefully)

color0 (black) and color8 (bright black / comments) are often deliberately dimmed; they may fall below 4.5:1 but must stay distinguishable from the background. Transparency, background images, displays, and color-vision differences all change actual appearance — computations assume opaque sRGB, and the delivery report must say so.

## 16-Color Semantic Checklist

| Index | Conventional meaning | Design rule |
|---|---|---|
| 0 | Black | A deep color near the background is fine, but it must stay distinguishable |
| 1 / 9 | Red: errors, deletions | ≥4.5:1 against background; 9 is the bright variant |
| 2 / 10 | Green: success, additions | same |
| 3 / 11 | Yellow: warnings | same |
| 4 / 12 | Blue: directories, links | same |
| 5 / 13 | Magenta: keywords etc. | same |
| 6 / 14 | Cyan | same |
| 7 | White: default foreground for many programs | Must check; usually close to foreground |
| 8 | Bright black: comments, secondary text | May fall below 4.5:1 but must stay readable |
| 15 | Bright white: default foreground in bright contexts | Must check |

Programs may invert foreground/background, emit true color, or use bold — beyond the math, preview in a real terminal; never convey state by color alone (pair red/green information with symbols).

## Do / Don't

Do: design dark and light backgrounds separately and compute contrast for each; keep saturated colors as small accents; offer the user 1–2 alternative directions.
Don't: flood the background with neon saturation; map emotions to colors one-to-one (red = anger, dark = negative); build a light theme by simply inverting the dark palette; adjust opacity or other non-color settings to chase a contrast number.

## External Sources

- APA interview on color psychology: https://www.apa.org/news/podcasts/speaking-of-psychology/color-emotion
- Systematic review and cross-national study: links at the top of this file
- WCAG contrast: https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html
