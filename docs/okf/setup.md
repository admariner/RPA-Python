---
type: Script
title: setup.py
description: setuptools packaging script that builds and publishes the tagui PyPI distribution.
resource: setup.py
tags: [packaging, setuptools, pypi]
timestamp: 2026-07-22T09:58:59Z
---

# setup.py

# Responsibility

Standard `setuptools.setup()` invocation that defines the `tagui` PyPI
package: ships `tagui.py` as its single module, and carries the package
metadata (author, license, classifiers, long description) shown on PyPI.

# Public Interface

```python
setup(name='tagui', version='1.50.0', py_modules=['tagui'], ...)
```

# Dependencies

- [tagui](tagui.md) — the module this script packages.

# Used By

Standalone build entry point (`python setup.py sdist`/`pip install .`),
not imported by other code.

# Notes

- Kept in lockstep with [`rpa_package/setup.py`](rpa-package/setup.md) and
  with `tagui.py`'s own `__version__` — all three currently pin `1.50.0`.
