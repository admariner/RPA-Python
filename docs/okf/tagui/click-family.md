---
type: Function
title: tagui.click / tagui.rclick / tagui.dclick
description: Click, right-click, or double-click a UI element or (x, y) screen coordinate.
resource: tagui.py#L1010
tags: [rpa, tagui, ui-interaction]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.click / tagui.rclick / tagui.dclick

# Responsibility

Three functions with an identical body, differing only in the TagUI
command word they send (`click`, `rclick`, `dclick`). Each requires
`init()` to have run, requires a non-empty `element_identifier`, checks
[`exist()`](exist-present.md) first (erroring with "cannot find ..." if
it doesn't), then sends the click command via [`send()`](send.md).

`test_coordinate`, if given as an `int`, is treated as the y-coordinate
and combined with `element_identifier` (treated as x) via
[`coord()`](config-utility.md) into an `"(x,y)"` string before anything
else runs — i.e. `click(600, 300)` and `click('(600,300)')` are
equivalent.

# Public Interface

```python
click(element_identifier=None, test_coordinate=None)    # left click
rclick(element_identifier=None, test_coordinate=None)    # right click
dclick(element_identifier=None, test_coordinate=None)    # double click
```

# Dependencies

- [tagui.exist-present](exist-present.md) — existence pre-check.
- [tagui.send](send.md) — issues the `click`/`rclick`/`dclick` TagUI
  command.
- [tagui.config-utility](config-utility.md) — `coord()` for the
  `(x, y)` overload.

# Used By

- [sample](../sample.md) — `r.click('ybar-search')`.
- `bin()` (see [table/upload/bin](table-upload-bin.md)) — clicks through
  the upload flow.

# Usage Example

```python
r.click('//*[@id="submit"]')      # by XPath
r.click(600, 300)                 # by x, y coordinate (visual automation)
r.rclick('Submit Form.png')       # by image / OCR text match
r.dclick('icon.png')
```

# Notes

- No dedicated identifier-type validation beyond `exist()` — any string
  accepted by TagUI's own `click`/`rclick`/`dclick` step works (XPath,
  CSS selector, DOM attribute, `.png`/`.bmp` image, OCR text, or
  coordinate).
