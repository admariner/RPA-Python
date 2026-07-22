---
type: Script
title: sample.py
description: Runnable usage example that searches Yahoo, takes a screenshot, and visits DuckDuckGo, demonstrating the core public API end to end.
resource: sample.py
tags: [example, rpa, tagui]
timestamp: 2026-07-22T09:58:59Z
---

# sample.py

# Responsibility

The canonical "getting started" example for the package (mirrored in the
project README). Demonstrates the typical call sequence — `init()`,
`url()`, `type()`, `read()`, `click()`, `wait()`, `snap()`, `close()` — and
documents, in comments, the different forms a UI element identifier can
take (XPath/CSS selector, DOM attribute, `.png`/`.bmp` visual identifier,
OCR fallback, x/y coordinates).

# Public Interface

Not an importable module — a standalone script run directly
(`python sample.py`).

# Dependencies

- [rpa-package/rpa](rpa-package/rpa.md) — imported as `import rpa as r`;
  exercises `init`, `url`, `type`, `read`, `click`, `wait`, `snap`,
  `close` from [tagui](tagui.md).

# Used By

Not imported elsewhere — a standalone runnable example.

# Usage Example

```python
import rpa as r
r.init()
r.url('https://ca.yahoo.com')
r.type('ybar-sbq', 'github')
search_text = r.read('ybar-sbq')
r.click('ybar-search')
r.wait(6.6)
r.snap('page', 'results.png')
r.close()
```

# Notes

- The trailing comments in this file are the primary in-repo reference for
  the different identifier syntaxes accepted by `click()`/`type()`/
  `read()`/`snap()` and friends (XPath/CSS/attribute selectors, `.png`/
  `.bmp` visual matches with OCR fallback, transparent-background images,
  and `x, y` coordinates).
