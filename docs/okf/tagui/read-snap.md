---
type: Function
title: tagui.read / tagui.snap
description: Read text/value from a UI element or screen region, or save a screenshot of an element/page/region to a file.
resource: tagui.py#L1168
tags: [rpa, tagui, ui-interaction]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.read / tagui.snap

# Responsibility

`read()` returns text/value content from `element_identifier`; `snap()`
saves a screenshot of it to `filename_to_save`. Both accept either a
normal identifier or up to three trailing integer coordinates that get
folded into a single `"(x1,y1)-(x2,y2)"` region string via
[`coord()`](config-utility.md) (a rectangular area of interest — e.g.
`r.read(200, 200, 600, 400)` reads the region between those two corners).
Both special-case the literal string `'page'` (case-insensitive) to skip
the [`exist()`](exist-present.md) check entirely, since the whole page
is always considered readable/snappable.

- `read(element_identifier, x1, y1, x2)` — needs all three of
  `test_coordinate1/2/3` as ints to build the region string; falls back
  to using `element_identifier` directly otherwise.
- `snap(element_identifier, filename_to_save, x1, y1, x2)` — same region
  logic, but only triggers if `element_identifier` **and**
  `filename_to_save` are themselves ints (i.e. the first two positional
  args are actually the region's first corner, and the real filename is
  the last argument, `test_coord3`). Also explicitly errors if a region
  is partially specified (`test_coord2` set but `test_coord3` missing).

# Public Interface

```python
read(element_identifier=None, test_coordinate1=None,
     test_coordinate2=None, test_coordinate3=None)
snap(element_identifier=None, filename_to_save=None,
     test_coord1=None, test_coord2=None, test_coord3=None)
```

# Dependencies

- [tagui.exist-present](exist-present.md), [tagui.send](send.md),
  [tagui.config-utility](config-utility.md) (`coord()`).

# Used By

- [sample](../sample.md) — `r.read('ybar-sbq')`,
  `r.snap('page', 'results.png')`, `r.snap('logo', 'logo.png')`.

# Usage Example

```python
search_text = r.read('ybar-sbq')
r.snap('page', 'results.png')                 # whole page
r.snap('logo', 'logo.png')                    # a specific element
r.read(200, 200, 600, 400)                    # region between two corners
r.snap(200, 200, 600, 400, 'results.png')     # screenshot of a region
```

# Notes

- `read()`/`snap()` are the two functions in the module whose region
  (rectangle) coordinate overload is genuinely tricky to read from the
  signature alone — the parameter names don't reveal that the last
  positional argument shifts meaning depending on the earlier arguments'
  types. Anyone modifying these functions should preserve the
  `isinstance(..., int)` type-sniffing exactly as-is.
