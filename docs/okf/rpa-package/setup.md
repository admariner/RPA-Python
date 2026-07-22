---
type: Script
title: rpa_package/setup.py
description: setuptools packaging script that builds and publishes the rpa PyPI distribution.
resource: rpa_package/setup.py
tags: [packaging, setuptools, pypi]
timestamp: 2026-07-22T09:58:59Z
---

# rpa_package/setup.py

# Responsibility

Standard `setuptools.setup()` invocation that defines the `rpa` PyPI
package: ships `rpa.py` as its single module, declares a runtime
dependency on `tagui>=1.50.0`, and carries the package metadata
(author, license, classifiers, long description) shown on PyPI.

# Public Interface

```python
setup(name='rpa', version='1.50.0', py_modules=['rpa'],
      install_requires=['tagui>=1.50.0'], ...)
```

# Dependencies

- [rpa-package/rpa](rpa.md) — the module this script packages.
- Declares an `install_requires` dependency on the separately-published
  `tagui` package (see [setup](../setup.md)).

# Used By

Standalone build entry point (`python setup.py sdist`/`pip install .`),
not imported by other code.

# Notes

- Kept in lockstep with the root [`setup.py`](../setup.md) and with
  `tagui.py`'s own `__version__` — all three currently pin `1.50.0`.
