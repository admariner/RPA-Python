---
type: Function
title: tagui.url
description: Navigate the browser to a webpage URL, or (called with no argument) return the current URL.
resource: tagui.py#L985
tags: [rpa, tagui, ui-interaction, browser]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.url

# Responsibility

Dual-purpose function distinguished purely by whether an argument was
passed. Requires `init(chrome_browser=True)` (the default). With a
`webpage_url` argument: auto-prepends `https://` if the string starts
with `www.`, requires the (possibly-rewritten) URL to start with
`http://`/`https://`, then sends it via [`send()`](send.md). With no
argument: sends `dump url() to rpa_python.txt` and returns the current
URL read back from that file.

# Public Interface

```python
url(webpage_url=None)  # navigates if given, else returns current URL (str)
```

# Dependencies

- [tagui.send](send.md) — both to navigate and to query the current URL.
- Uses `_started()`, `_chrome()`, `_esq()`, `_tagui_output()` from
  [tagui](../tagui.md).

# Used By

- [sample](../sample.md) — `r.url('https://ca.yahoo.com')`, then later
  `r.url('https://duckduckgo.com')`.
- [tagui.table-upload-bin](table-upload-bin.md) — `bin()` saves and
  restores the current URL around its upload flow using
  `url()`/`url(original_url)`.

# Usage Example

```python
r.url('https://ca.yahoo.com')   # navigate
r.url('www.example.com')        # 'www.' auto-upgraded to 'https://'
current = r.url()               # query current URL
```
