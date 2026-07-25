# Directory Update Log

## 2026-07-22
- **Added**: Deepened the initial scan after feedback that the bundle was
  too shallow — 15 new `tagui/` Function concepts covering the ~58
  public functions previously only listed in `tagui.md`'s Public
  Interface table (grouped by shared implementation pattern:
  `exist-present`, `click-family`, `hover`, `type-select`, `read-snap`,
  `url`, `frame-popup`, `dom-run`, `table-upload-bin`, `visual-input`,
  `mouse-position`, `clipboard`, `config-utility`, `state-readers`,
  `misc`, `string-utility`), plus `live-mode-protocol` (the echo-marker
  instruction protocol behind `init`/`send`/`close`) and
  `setup-internals` (the private per-OS patch/delta helpers). Updated
  `tagui.md` and `tagui/index.md` to link into the new concepts instead
  of inlining bare signatures.
- **Initial scan**: Generated the OKF bundle for `marcelocecin/RPA-Python`
  at commit `8259b4c`. Documented the `tagui` module (1 Module concept, 7
  Function concepts for entry points with non-trivial logic), the `rpa`
  distributable package, and the two `setup.py` build scripts plus
  `sample.py`.
