# Design-system `--preview` demo

This branch walks through `search.py --design-system --preview`: a self-contained `preview.html` for the generated palette, type pairing, buttons, card, form, style, effects, and anti-patterns.

The feature branch is `feat/design-system-preview`. This orphan branch holds only recorded evidence.

## Commands that produced the screenshots

Exact invocations and stdout live in `commands.log`. The runs that wrote the pages shown in `screenshots/` were:

```bash
python3 src/ui-ux-pro-max/scripts/search.py "fintech dashboard" --preview
# exits 2: --preview requires --design-system

python3 src/ui-ux-pro-max/scripts/search.py "fintech dashboard" --design-system --persist --preview -p "Ledger" --output-dir /tmp/ds-demo/previews-cli

python3 src/ui-ux-pro-max/scripts/search.py "kids education app" --design-system --persist --preview -p "Playroom" --output-dir /tmp/ds-demo/previews-cli

python3 src/ui-ux-pro-max/scripts/search.py "dark mode developer tool" --design-system --persist --preview -p "Night IDE" --output-dir /tmp/ds-demo/previews-cli

python3 src/ui-ux-pro-max/scripts/search.py "kids education app" --design-system --preview -p '<script>alert(1)</script>' --output-dir /tmp/ds-demo/previews-cli/hostile

python3 src/ui-ux-pro-max/scripts/search.py "fintech dashboard" --design-system --persist --preview -p "Ledger" --output-dir /tmp/ds-demo/previews-cli
# skips existing MASTER.md and preview.html

python3 src/ui-ux-pro-max/scripts/search.py "fintech dashboard" --design-system --persist --preview --force -p "Ledger" --output-dir /tmp/ds-demo/previews-cli
```

Chromium (Playwright) then opened each `preview.html` as a `file://` URL and captured full-page plus viewport PNGs. One Night IDE capture aborted every non-`file:` request so Google Fonts could not load.

## What the screenshots show

| File | What you are looking at |
| --- | --- |
| `screenshots/fintech-ledger.png` | Ledger persist preview: dark palette swatches with contrast ratios, type scale, button states, card, form |
| `screenshots/kids-playroom.png` | Playroom persist preview (kids education app) |
| `screenshots/dark-night-ide.png` | Night IDE persist preview on the generated dark surfaces |
| `screenshots/dark-night-ide-offline.png` | Same Night IDE page with networking disabled; layout still paints with system-font fallbacks |
| `screenshots/hostile-name.png` | `--project-name '<script>alert(1)</script>'` rendered as literal escaped text in the heading, not as a script tag |
| `*-viewport.png` | 1440×900 crop of the same pages |

`grep` of the hostile HTML in `commands.log` shows `&lt;script&gt;alert(1)&lt;/script&gt;` in the title and headings.

## Video

`out.mp4` is 24 s, 1920×1080, 30 fps, 720 frames, H.264 yuv420p. Frames, in order:

1. **0–4 s** — Ledger `preview.html`, slow pan (swatches, type, buttons).
2. **4–8 s** — Playroom `preview.html`, slow pan.
3. **8–12 s** — Night IDE `preview.html`, slow pan on the generated dark surfaces.
4. **12–15 s** — Large card from `commands.log`: `--preview` without `--design-system` → `search.py: error: --preview requires --design-system` / `exit: 2`. Held ~3 s. Label: **Animated replay of real captured command output**.
5. **15–18 s** — Large card: re-run without `--force` skips `MASTER.md` and `preview.html`; `--force` overwrites both. Held ~3 s.
6. **18–21 s** — Large card: hostile `-p '<script>alert(1)</script>'` plus `grep` showing `&lt;script&gt;` in the title and headings. Held ~3 s.
7. **21–24 s** — Ledger palette frame held (swatches + AA/AAA contrast badges).

`preview.gif` is the same 24 s at 960×540, 12 fps, last frame held for the final ~2 s, palette-reduced under 5 MB.

ffprobe output: `logs/ffprobe.txt`.
