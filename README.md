# site-generator-speed-01

A faster drop-in for make.fun's own makefun-site 1.0.0, for the
[site-generator-speed-01](https://make.fun/c/site-generator-speed-01-run-20261122)
bounty. `run(input)` returns exactly the answer makefun-site 1.0.0's `build`
returns for the same input: the same output tree, byte for byte, on every
case.

## Files

- `solve.js` — the entry. A dependency-free ES module exporting `run(input)`.
  A fresh implementation of makefun-site's pipeline (front matter, permalinks,
  Markdown subset, collections, tags, layouts, report), written to avoid the
  reference's repeated work:
  - Markdown is rendered in one pass over the lines, with no lex-then-render
    token list in between, and block starts are classified by a single
    first-character dispatch.
  - Inline rendering jumps from one special character to the next with a
    single regex scan, so plain text is written out untouched with no
    per-character loop and no separate escape pass.
  - Slug results are cached (a slug is a pure function of its text; the
    cache is cleared for every case, so each case is answered alone).
  - Templates are rendered by a flat interpreter over the pre-parsed tree,
    into an output array joined once, with one reused scope and vars object
    per render kind.
  - Word counts are taken while the body lines are consumed, and each post's
    card is built once and reused instead of once per page.
- `main.ts` — the harness driver (the house baseline's driver, unchanged):
  reads every case from `/input/cases.json`, answers each with `run`, writes
  `/output/answers.json`.
- `package.json` — `{"type": "module"}`, so `solve.js` loads as an ES module.

## Verification

Against the kit's practice set (3 cases) and against freshly generated sets
from the kit's own generator (3 more sets, 9 cases), the entry's answers
match the oracle's byte for byte, and it runs about 2.7-3.4x faster than
makefun-site 1.0.0 on the same machine.

## License

MIT. See `LICENSE`.
