---
type: Function
title: tagui.type / tagui.select
description: Type text into a UI element/coordinate, or select an option in a dropdown/select box.
resource: tagui.py#L1098
tags: [rpa, tagui, ui-interaction]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.type / tagui.select

# Responsibility

`type()` enters `text_to_type` into `element_identifier`; `select()`
picks `option_value` in `element_identifier`. Both require `init()`, a
non-empty target, and a non-empty value, check
[`exist()`](exist-present.md), then delegate to [`send()`](send.md)
sending `type <id> as <text>` / `select <id> as <value>`.

- `type()`'s `(x, y)` overload: pass `test_coordinate` as an int — it's
  actually detected on `text_to_type` being an int
  (`isinstance(text_to_type, int)`), which then means
  `element_identifier` is treated as x, `text_to_type` as y, and the
  *third* positional argument (named `test_coordinate` in the signature)
  becomes the actual text to type. Concretely:
  `type(600, 300, 'Mario')` → clicks/types at `(600, 300)` the text
  `'Mario'`.
- `select()`'s coordinate overload similarly repurposes
  `option_value`/`test_coordinate1`/`test_coordinate2` as x1/x2/y2 —
  `select(600, 300, 600, 400)` selects the dropdown at `(600,300)` to the
  option represented by `(600,400)`.
- `select()` additionally rejects `'page.png'`/`'page.bmp'` for either
  argument (they're not valid selectable identifiers), and separately
  requires `init(visual_automation=True)` if either argument is a
  `.png`/`.bmp` image identifier.

# Public Interface

```python
type(element_identifier=None, text_to_type=None, test_coordinate=None)
select(element_identifier=None, option_value=None,
       test_coordinate1=None, test_coordinate2=None)
```

# Dependencies

- [tagui.exist-present](exist-present.md), [tagui.send](send.md) — same
  guard/dispatch pattern as the [click family](click-family.md).
- Uses `_esq()`/`_sdq()` from [tagui](../tagui.md) to escape the text/
  option value and identifier respectively before sending.

# Used By

- [sample](../sample.md) — `r.type('ybar-sbq', 'github')`.

# Usage Example

```python
r.type('ybar-sbq', 'github')            # '[enter]' / '[clear]' are special values
r.type(600, 300, 'Mario')               # by coordinate
r.select('#country', 'Singapore')
```

# Notes

- `'[enter]'` and `'[clear]'` are documented (in `sample.py`'s comments)
  as special values for `text_to_type` — enter key and clear-field,
  respectively — interpreted by TagUI's own `type` step, not by this
  Python wrapper.
