Title: feat(design-system): export the generated design system as design tokens

## What does this PR change?

`--design-system` can now emit machine-readable tokens: `--tokens dtcg` prints a W3C DTCG JSON document (`$value` / `$type`, `color`, `font`, and a `semantic` layer), and `--tokens css` prints `:root` custom properties using the same names `generate-tokens.cjs` already produces (`--color-primary`, `--font-heading`, …). With `--persist`, `tokens.json` is written next to `MASTER.md` (and `tokens.css` when the format is `css`), listed in the master file, and subject to the existing skip-if-exists / `--force` / `--output-dir` rules. Tokens stay master-level: `--page` does not write a page token file.

`--tokens` without `--design-system` is a hard argparse error.

`cli/tests/e2e/template-script-paths.spec.ts` `SEARCH_COMMAND_COUNT` is 22 so it matches the five `--tokens` command lines added to `templates/base/skill-content.md`. `relevance-thresholds.json` `runtimeFingerprint` is refreshed because `design_system.py` changed (that file is part of the hashed runtime).

## Why?

`--design-system --persist` already writes `MASTER.md`, but hex values and font names still have to be copied by hand into CSS variables or a token file. That copy step is where the live stylesheet drifts from the generated system. This wires the generator to the DTCG JSON / CSS path the bundled `design-system` skill already consumes.

## Checklist

- [x] Changes were made in `src/ui-ux-pro-max/` (source of truth), not directly in `.claude/` or `.factory/`
- [x] Ran `npm run sync:assets && npm run check:assets` in `cli/` if data/scripts/templates changed
- [x] Added or updated tests if behavior changed (`.claude/skills/*/scripts/tests/`, `cli/tests/e2e/`)
- [x] Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`, `docs:`, etc.)
- [x] This PR targets a feature branch, not pushed directly to `main`

## Behavior at the edges

- Dark systems (existing `_derive_dark_palette` / `color_mode`) emit the chosen dark hex values and mark the mode in `$description`.
- Every color is a 6-digit hex. Tests check on-color pairs with the generator's `_contrast_ratio`; there is no second contrast implementation.
- Persist skip: if `tokens.json` / `tokens.css` already exist and `--force` is off, those files are left alone (same rule as `MASTER.md`).
- `--page` still writes page markdown only; token files are not created per page.
- Feeding `--tokens dtcg` JSON into `generate-tokens.cjs` produces CSS that is byte-identical to `--tokens css`.

## Tests run

From `cli/`:

- `npm run verify:data` — pass (CSV 35 files; semantic 12 domains / 22 stacks; agent-guide 25 platforms; catalog summary current; `test:python` 198 tests OK; relevance eval; smoke 12/12 domains and 22/22 stacks; assets in sync)
- `npm run typecheck` — pass
- `npm run build` — pass
- `npm run check:assets` — pass (`Assets are in sync.`)
- `npx playwright test` — pass, 56 passed

Also:

- `python3 -m unittest discover -s src/ui-ux-pro-max/scripts/tests -p 'test_*.py' -v` — 198 tests OK (includes `test_design_system_tokens.py`: DTCG shape, hex, contrast, CSS names, persist, skip/force, `--page` does not emit page tokens, CLI, argparse, Node round-trip)
- `python3 -m pytest .claude/skills -q --tb=no` — 336 passed, 8327 subtests passed

## What was not verified

- Translated README files
- Installing the skill into a third-party editor and generating a live product UI from the tokens there (the demo page only consumes `var(--color-*)` / `var(--font-*)` from the emitted CSS)
- `gallery/` npm test (this change does not touch gallery sources)

## Demo

![demo](https://raw.githubusercontent.com/mvanhorn/pr-demo-assets/main/osc-bulk-1115b/design-system-tokens/preview.gif)

Full recording (MP4): https://raw.githubusercontent.com/mvanhorn/pr-demo-assets/main/osc-bulk-1115b/design-system-tokens/out.mp4
