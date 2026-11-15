Title: feat(design-system): write a self-contained preview page for the generated system

## What does this PR change?

Adds `--preview` to `search.py` so a generated design system can be opened as a static `preview.html`: palette swatches with WCAG contrast (AA/AAA/Fail from the generator's `_contrast_ratio`), heading/body type scale, primary/secondary/accent/destructive buttons in default/hover/focus-visible/disabled, a card, a labeled form field with help and error, plus the style name, key effects, and anti-patterns. The page uses inline CSS only (no JS framework, no Tailwind CDN). Google Fonts are linked when the pairing has a URL; `system-ui` fallbacks keep it readable offline. Dark systems render on the surfaces they resolved. `prefers-reduced-motion` disables the hover transform.

`--preview` is valid only with `--design-system`. With `--persist` it writes next to `MASTER.md`. Without persist it writes to `--output-dir/preview.html`, or to a temp file whose path is printed. Existing files are skipped unless `--force` is set, same as Master. CSV and `--project-name` values are HTML-escaped.

## Why?

`--design-system --persist` already writes `MASTER.md`. That file describes the composition; it does not show whether the palette, type pairing, and components hold together. `preview.html` is that missing check, still generated only from the system the search already produced.

## Checklist

- [x] Changes were made in `src/ui-ux-pro-max/` (source of truth), not directly in `.claude/` or `.factory/`
- [x] Ran `npm run sync:assets && npm run check:assets` in `cli/` if data/scripts/templates changed
- [x] Added or updated tests if behavior changed (`.claude/skills/*/scripts/tests/`, `cli/tests/e2e/`)
- [x] Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`, `docs:`, etc.)
- [x] This PR targets a feature branch, not pushed directly to `main`

`--preview` without `--design-system` exits 2 (`parser.error`). Re-running persist without `--force` leaves both Master and preview untouched; `--force` overwrites. A `--project-name` of `<script>alert(1)</script>` is stored as `&lt;script&gt;alert(1)&lt;/script&gt;` in the title and headings.

Tests and checks that passed:

- `python3 -m unittest discover -s src/ui-ux-pro-max/scripts/tests -p 'test_*.py' -v` — 195 tests, OK (includes 15 new preview tests: structure, escaping, contrast vs generator, dark surfaces, CLI error/path/`--force`)
- `npm --prefix cli run verify:data` — pass (CSV/semantic/agent-guide/catalog, unittest, relevance, 12/12 domain smoke, 22/22 stack smoke, assets in sync)
- `python3 -m pytest .claude/skills -v` — 333 passed, 8304 subtests passed
- `npx playwright test` (in `cli/`) — 56 passed. `SEARCH_COMMAND_COUNT` in `cli/tests/e2e/template-script-paths.spec.ts` is 18 (was 17) so the new `--preview` command in skill-content is counted.
- `npm --prefix cli run typecheck` — pass
- `npm --prefix cli run build` — pass (TypeScript compiler fallback; `bun` is not installed in this environment)
- gallery `npm test` — 8 pass; `npm run build` — pass
- axe-core via Playwright on four generated pages (Ledger, Playroom, Night IDE, hostile name) — 0 violations, 21 passes each

Not verified: the `bun` runtime path of `cli/scripts/build.mjs` (bun missing, tsc fallback used).

## Demo

![demo](https://raw.githubusercontent.com/mvanhorn/pr-demo-assets/main/osc-bulk-1115b/design-system-preview/preview.gif)

Full recording (MP4): https://raw.githubusercontent.com/mvanhorn/pr-demo-assets/main/osc-bulk-1115b/design-system-preview/out.mp4
