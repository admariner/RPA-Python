---
type: Function
title: tagui.table / tagui.upload / tagui.bin
description: Export an HTML table to CSV, upload a file through a file-input element, or upload a file to a paste/share service (deprecated) and return its link.
resource: tagui.py#L1399
tags: [rpa, tagui, ui-interaction, deprecated]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.table / tagui.upload / tagui.bin

# Responsibility

- `table(element_identifier, filename_to_save)`: exports an HTML
  `<table>` element to a CSV file. Requires both arguments non-empty,
  checks [`exist()`](exist-present.md), then sends
  `table <id> to <filename>` via [`send()`](send.md).
- `upload(element_identifier, filename_to_upload)`: uploads a local file
  through a file-input UI element. Same guard pattern, sends
  `upload <id> as <filename>`.
- `bin(file_to_bin, password, server)`: **deprecated** (per the project
  README) — drives a full browser flow against a hardcoded third-party
  paste service (`https://tebel.org/bin/` by default): saves the current
  URL, navigates to `server`, verifies a `#message` element exists (else
  errors "cannot connect to"), types the filename and optional password
  into the form, clicks attach/upload/send, [`upload()`](#)s the actual
  file, reads back the resulting share link from `#pastelink a`'s `href`
  attribute, restores the original URL, and returns the link (or `''` on
  failure).

# Public Interface

```python
table(element_identifier=None, filename_to_save=None)              # returns True/False
upload(element_identifier=None, filename_to_upload=None)            # returns True/False
bin(file_to_bin=None, password=None, server='https://tebel.org/bin/')  # returns share URL or ''
```

# Dependencies

- [tagui.exist-present](exist-present.md), [tagui.send](send.md) — used
  by all three.
- `bin()` additionally calls [tagui.url](url.md), and reuses `upload()`,
  [click family](click-family.md)'s `click()`, and
  [type/select](type-select.md)'s `type()` internally to drive its
  browser flow, plus [read/snap](read-snap.md)'s `read()` to fetch the
  resulting link.

# Used By

Standalone calls; `bin()`'s internal reuse of `click`/`type`/`upload`/
`read`/`url` is its only cross-function dependency in this module.

# Usage Example

```python
r.table('//table[@id="results"]', 'results.csv')
r.upload('#file-input', '/path/to/file.pdf')
```

# Notes

- `bin()` is explicitly called out as deprecated in the project's
  README — it depends on a specific external service
  (`tebel.org/bin`) staying online and its DOM structure staying stable;
  avoid it in new code.
