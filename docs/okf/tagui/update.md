---
type: Function
title: tagui.update
description: Builds a self-contained update.py that embeds the latest TagUI delta files (base64-zipped) for updating an existing offline/air-gapped installation.
resource: tagui.py#L683
tags: [rpa, tagui, offline-deployment]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.update

# Responsibility

Companion to [`pack()`](pack.md) for machines that already have RPA for
Python installed offline. Downloads the latest `tagui.py` and TagUI
"delta" files (same list as [`_tagui_delta()`](../tagui.md)), zips them,
base64-encodes the zip, and writes it into a generated `update.py` script
(along with unzip/install logic) that can be copied or emailed to the
offline target and run there with no separate download step.

# Public Interface

```python
update()  # no arguments; returns True/False
```

# Dependencies

- [tagui.download](download.md) — fetches the latest `tagui.py` and each
  delta file from the project's GitHub-hosted mirrors.
- Uses `load()`, `dump()`, `write()`, and `unzip()` (via the generated
  `update.py`'s embedded call to `r.unzip(...)`) from
  [tagui](../tagui.md).

# Used By

Standalone entry point — generates a script meant to be run independently
on the target machine, not called by other functions in this module.

# Usage Example

```python
import rpa as r
r.update()
# copy or email the generated update.py to the offline machine, then:
# python update.py
```

# Notes

- The generated `update.py` is fully self-contained: it embeds the update
  zip as a base64 string literal and, when run, decodes/unzips it into the
  TagUI installation directory and moves the refreshed `rpa.py` into the
  installed package folder.
- Reads the target version number out of the freshly downloaded
  `tagui.py`'s `__version__` string to report/name the update.
