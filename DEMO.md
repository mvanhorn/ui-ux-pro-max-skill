# Demo: design-system token export

Recorded from the real CLI on `feat/design-system-tokens` (`1a3657209d7957d36cd98441f05004fae6797602`).

The MP4 is an animated replay of exact lines from `commands.log`, labeled **Animated replay of real captured command output**. Each proving beat is held about 3 seconds. Frames, in order:

1. **0.0–3.2 s — DTCG excerpt.** `search.py "fintech dashboard" --design-system --tokens dtcg` showing `$schema`, `$description` (dark mode), `color.primary`, `color.on-primary`, and `font.heading`.
2. **3.2–6.4 s — CSS `:root`.** The same query with `--tokens css`: semantic custom properties (`--color-primary`, `--font-heading`, …).
3. **6.4–9.6 s — persisted files.** `ls` of `design-system/ledgerly` with `tokens.json` next to `MASTER.md`, then the MASTER.md token-file lines.
4. **9.6–13.0 s — round-trip, held ~3 s.** `generate-tokens.cjs --config tokens.json` then `diff` against `--tokens css` (`[exit 0]`, empty diff).
5. **13.0–16.2 s — dark-mode `$description`.** `"dark mode developer tool"` prints `Design tokens for DARK MODE DEVELOPER TOOL (dark mode).`
6. **16.2–21.0 s — argparse refusal.** `--tokens` without `--design-system` ends with `search.py: error: --tokens requires --design-system` and `[exit 2]`.
7. **21.0–24.0 s — tokens-styled page, held ~3 s.** Static HTML that uses only `var(--color-*)` / `var(--font-*)` from `tokens.css`.

## Commands captured

See `commands.log` (quoted argv, real stdout/stderr, exit codes):

```
python3 src/ui-ux-pro-max/scripts/search.py "fintech dashboard" --design-system --tokens dtcg
python3 src/ui-ux-pro-max/scripts/search.py "fintech dashboard" --design-system --tokens css
python3 src/ui-ux-pro-max/scripts/search.py "fintech dashboard" --design-system --persist --tokens dtcg -p "Ledgerly" --output-dir /tmp/demo-run
ls -la /tmp/demo-run/design-system/ledgerly
cat /tmp/demo-run/design-system/ledgerly/tokens.json
grep -n -i token /tmp/demo-run/design-system/ledgerly/MASTER.md
node cli/assets/skills/design-system/scripts/generate-tokens.cjs --config /tmp/demo-run/design-system/ledgerly/tokens.json -o /tmp/demo-run/from-cjs.css
diff -u /tmp/demo-run/tokens-css.css /tmp/demo-run/from-cjs.css
python3 src/ui-ux-pro-max/scripts/search.py "dark mode developer tool" --design-system --tokens dtcg
python3 src/ui-ux-pro-max/scripts/search.py "dark mode developer tool" --design-system --tokens css
python3 src/ui-ux-pro-max/scripts/search.py "fintech dashboard" --tokens dtcg
```

## Media

| File | Spec |
|---|---|
| `out.mp4` | 1920×1080, 30 fps, 720 frames, 24.00 s, H.264 yuv420p |
| `preview.gif` | 1280×720, 12 fps, 288 frames, 24.00 s, 543 KB |

`preview.gif` is derived from `out.mp4` with ffmpeg `palettegen` / `paletteuse`. The last ~3 s of both files hold the tokens-styled page. Probe output is in `logs/ffprobe.txt`.

## Screenshots

- `screenshots/ledgerly-tokens-desktop.png` — 1920×1080 page using only CSS variables from the generated tokens
- `screenshots/ledgerly-tokens-mobile.png` — 390×844 viewport of the same page
