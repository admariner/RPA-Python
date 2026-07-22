---
type: Function
title: tagui.pack
description: Bundles the local TagUI installation and this package's own source into rpa_python.zip / rpa.py for offline ("air-gapped") deployment.
resource: tagui.py#L643
tags: [rpa, tagui, offline-deployment]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.pack

# Responsibility

Prepares the two files an air-gapped target machine needs to install RPA
for Python without internet access: `rpa_python.zip` (a full TagUI
installation, including the SikuliX/Jython dependency needed for visual
automation) and a copy of `tagui.py` renamed to `rpa.py`. Run this on a
machine with internet access, then copy both files to the offline target
and follow up with [`setup()`](setup.md) there (triggered automatically by
[`init()`](init.md) when it detects `rpa_python.zip` next to it).

# Public Interface

```python
pack()  # no arguments; returns True/False
```

# Dependencies

- [tagui.init](init.md) / [tagui.close](close.md) — pack() forces a
  `init(False, False)` + `close()` cycle first to guarantee TagUI is
  installed and its delta files are synced before zipping.
- [tagui.download](download.md) — fetches the SikuliX Jython jar (and, on
  Windows, the Visual C++ Redistributable) into the installation before
  zipping.

# Used By

Standalone entry point, invoked directly by a user/script preparing an
offline deployment — not called by other functions in the module.

# Usage Example

```python
import rpa as r
r.pack()
# copy the generated rpa_python.zip and rpa.py to the offline machine,
# then there: import rpa as r; r.init()
```

# Notes

- Companion to [`update()`](update.md): `pack()` is for a first offline
  install, `update()` is for refreshing an install that was originally
  deployed via `pack()`.
