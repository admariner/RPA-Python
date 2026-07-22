---
type: Function
title: tagui.mouse_xy / tagui.mouse_x / tagui.mouse_y
description: Return the current mouse pointer position on screen, as a coordinate string or individual x/y integers (visual automation only).
resource: tagui.py#L1780
tags: [rpa, tagui, visual-automation]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.mouse_xy / tagui.mouse_x / tagui.mouse_y

# Responsibility

Three read-only queries with the same shape: require `init()` and
`init(visual_automation=True)`, then `dump <call>() to rpa_python.txt`
and read the result back. `mouse_xy()` returns the raw TagUI-formatted
string; `mouse_x()`/`mouse_y()` each additionally coerce the result to
`int`.

# Public Interface

```python
mouse_xy()  # returns coordinate string, e.g. "(600,300)"
mouse_x()   # returns int
mouse_y()   # returns int
```

# Dependencies

- [tagui.send](send.md) — issues `dump mouse_xy()/mouse_x()/mouse_y() to rpa_python.txt`.
- Uses `_started()`, `_visual()`, `_tagui_output()` from
  [tagui](../tagui.md).

# Used By

Standalone queries; typically paired with the [click family](click-family.md)
or [hover](hover.md) for coordinate-relative automation.

# Usage Example

```python
r.init(visual_automation=True)
x, y = r.mouse_x(), r.mouse_y()
```
