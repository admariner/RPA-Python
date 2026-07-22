---
type: Function
title: tagui.get_text / tagui.del_chars
description: Pure string helpers with no TagUI/process interaction — substring extraction between two delimiters, and character stripping.
resource: tagui.py#L1862
tags: [rpa, tagui, string-utility]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.get_text / tagui.del_chars

# Responsibility

Two pure, self-contained string functions with no dependency on
`init()`, `send()`, or any TagUI process state — usable standalone for
post-processing whatever [`read()`](read-snap.md)/[`dom()`](dom-run.md)
returned.

- `get_text(source_text, left, right, count=1)` — returns the (stripped)
  substring between the `count`-th occurrence of `left` and the next
  occurrence of `right` after it. Returns `''` if either delimiter isn't
  found at the requested occurrence.
- `del_chars(source_text, characters)` — removes every character in
  `characters` from `source_text` (iterates and calls `str.replace` per
  character).

# Public Interface

```python
get_text(source_text=None, left=None, right=None, count=1)  # -> str
del_chars(source_text=None, characters=None)                 # -> str
```

# Dependencies

None — pure string operations, no imports beyond built-ins.

# Used By

Standalone utilities; not called elsewhere in this module, intended for
use in caller scripts to post-process scraped text.

# Usage Example

```python
html = r.read('page')
price = r.get_text(html, '$', '<', count=1)
clean = r.del_chars('a-b-c', '-')   # -> 'abc'
```
