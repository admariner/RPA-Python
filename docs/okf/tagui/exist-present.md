---
type: Function
title: tagui.exist / tagui.present
description: Check whether a UI element, screen keyword, or coordinate exists/is present on screen, with special handling for visual-automation keywords and OCR fallback.
resource: tagui.py#L946
tags: [rpa, tagui, ui-interaction, visual-automation]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.exist / tagui.present

# Responsibility

Both functions answer "can I interact with this identifier?" and share
identical special-case logic; the only difference is that `exist()`
delegates to TagUI's own `exist()` JS helper (which polls up to the
current [`timeout()`](../tagui.md) before giving up — see
[`_tagui_local_js`](setup-internals.md)), while `present()` checks once,
immediately, with no wait. Almost every UI-interaction function
(`click`, `type`, `read`, `snap`, ...) calls `exist()` first as a
guard before sending its real instruction.

Special-cased `element_identifier` values, handled identically in both
functions:
- `'page.png'` / `'page.bmp'` (case-insensitive) — always exist/present if
  `init(visual_automation=True)` was used, since "the computer screen
  always exists"; errors otherwise.
- Any other `.png`/`.bmp` string — requires visual automation; errors if
  not enabled.
- A literal `"(x,y)"` or `"(x,y,z)"` coordinate string with no alphabetic
  characters (as produced by [`coord()`](config-utility.md)) — always
  exists/present under visual automation, on the assumption on-screen
  coordinates are always valid.
- Anything else — sent to TagUI as a real existence check
  (`exist(...)`/`present(...)` JS call via [`send()`](send.md)), whose
  result comes back through the `rpa_python.txt` output file.

# Public Interface

```python
exist(element_identifier=None)     # returns True/False, waits up to timeout()
present(element_identifier=None)   # returns True/False, no wait
```

# Dependencies

- [tagui.send](send.md) — issues the underlying `exist(...)`/`present(...)`
  TagUI JS calls and reads the boolean result back via
  `_tagui_output()`.
- Uses `_visual()`, `_started()`, `_sdq()` from [tagui](../tagui.md).

# Used By

- Nearly every UI-interaction concept — [click family](click-family.md),
  [hover](hover.md), [type/select](type-select.md), [read/snap](read-snap.md),
  [table/upload/bin](table-upload-bin.md) — calls `exist()` before acting,
  to fail fast with a clear error instead of letting TagUI time out
  silently.

# Usage Example

```python
if r.exist('//*[@id="submit"]'):
    r.click('//*[@id="submit"]')

if r.present('page.png'):   # requires init(visual_automation=True)
    print('visual automation is active')
```

# Notes

- `present()`'s "return False for a coordinate/keyword with no visual
  automation" branches print an error via `show_error()` even though the
  function is often used just to probe (`if r.present(...)`) — callers
  relying on a silent `False` should be aware output may still appear on
  stdout / raise if `error(True)` is set.
