---
type: Module
title: rpa_package/rpa
description: The rpa PyPI package's module — a one-line re-export of tagui.py's entire public API for backward compatibility.
resource: rpa_package/rpa.py
tags: [rpa, tagui, packaging, backward-compatibility]
timestamp: 2026-07-22T09:58:59Z
---

# rpa_package/rpa

# Responsibility

Lets users write `import rpa as r` (the package's advertised usage, see
[sample](../sample.md)) instead of `import tagui`. The file is generated
by [`tagui.pack()`](../tagui/pack.md) as a copy of the root `tagui.py`,
then re-exports everything from it with `from tagui import *` so the two
distributions (`tagui` and `rpa` on PyPI) stay behaviorally identical.

# Public Interface

```python
from tagui import *
```

Re-exports the entire public interface documented in
[tagui](../tagui.md) (module and per-function concepts under
[tagui/](../tagui/)).

# Dependencies

- [tagui](../tagui.md) — re-exports all of its public symbols.

# Used By

- [rpa-package/setup](setup.md) — packages this file as the `rpa` PyPI
  distribution (`py_modules=['rpa'], install_requires=['tagui>=1.50.0']`).

# Usage Example

```python
import rpa as r
r.init()
```

# Notes

- Kept in sync with `tagui.py` by [`pack()`](../tagui/pack.md)
  (`shutil.copyfile(.../tagui.py, 'rpa.py')`) rather than maintained
  independently — do not hand-edit without also updating `tagui.py`.
