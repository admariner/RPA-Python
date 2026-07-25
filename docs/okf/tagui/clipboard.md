---
type: Function
title: tagui.clipboard
description: Get or set the OS clipboard's text content (visual automation only).
resource: tagui.py#L1819
tags: [rpa, tagui, visual-automation]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.clipboard

# Responsibility

Dual-purpose like [`url()`](url.md): requires `init(visual_automation=True)`.
Called with no argument, it dumps and returns the current clipboard
content (`dump clipboard() to rpa_python.txt`). Called with
`text_to_put`, it instead sends a raw `js clipboard('<text>')` instruction
that sets it, with single quotes swapped to the `[BACKSLASH_QUOTE]`
marker that [`send()`](send.md) unescapes.

# Public Interface

```python
clipboard(text_to_put=None)  # returns current clipboard text, or True/False when setting
```

# Dependencies

- [tagui.send](send.md).
- Uses `_started()`, `_visual()`, `_tagui_output()` from
  [tagui](../tagui.md).

# Used By

Standalone call; not depended on by other documented functions.

# Usage Example

```python
r.init(visual_automation=True)
r.clipboard('hello world')
current = r.clipboard()
```
