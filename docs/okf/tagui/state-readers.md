---
type: Function
title: tagui.count / tagui.title / tagui.text / tagui.timer
description: Read-only queries against the current page — matching-element count, page title, visible text, and elapsed flow time.
resource: tagui.py#L1729
tags: [rpa, tagui, ui-interaction, browser]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.count / tagui.title / tagui.text / tagui.timer

# Responsibility

Four simple read-only queries, all following the same
`send('dump <call>() to rpa_python.txt'); return _tagui_output()` shape:

- `count(element_identifier)` — number of elements matching the
  identifier (requires `init(chrome_browser=True)`; returns `0` without
  erroring if not started or no identifier given).
- `title()` — the current page's `<title>` (requires
  `init(chrome_browser=True)`).
- `text()` — the current page's visible text content (requires
  `init(chrome_browser=True)`).
- `timer()` — elapsed seconds of the current TagUI flow, as a float; the
  only one of the four that does **not** require `chrome_browser`.

# Public Interface

```python
count(element_identifier=None)  # -> int
title()                          # -> str
text()                           # -> str
timer()                          # -> float
```

# Dependencies

- [tagui.send](send.md), `_tagui_output()` from [tagui](../tagui.md).

# Used By

Standalone queries; not depended on by other documented functions.

# Usage Example

```python
r.url('https://example.com')
print(r.title(), r.count('//a'))
elapsed = r.timer()
```
