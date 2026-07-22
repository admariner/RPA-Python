---
type: Function
title: tagui.hover
description: Hover the mouse/pointer over a UI element or (x, y) screen coordinate.
resource: tagui.py#L1076
tags: [rpa, tagui, ui-interaction]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.hover

# Responsibility

Same structure as the [click family](click-family.md): requires `init()`,
requires a non-empty `element_identifier`, resolves an optional integer
`test_coordinate` into an `(x, y)` string via
[`coord()`](config-utility.md), checks [`exist()`](exist-present.md),
then sends a `hover` TagUI command.

# Public Interface

```python
hover(element_identifier=None, test_coordinate=None)
```

# Dependencies

- [tagui.exist-present](exist-present.md), [tagui.send](send.md),
  [tagui.config-utility](config-utility.md) — same roles as in the
  [click family](click-family.md).

# Used By

Standalone UI-interaction call; no other documented function in this
module depends on it.

# Usage Example

```python
r.hover('menu-item')
r.hover(400, 250)   # x, y coordinate, visual automation
```
