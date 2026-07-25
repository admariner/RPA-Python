---
type: Function
title: tagui.setup
description: Downloads and installs TagUI into the user's home folder (or APPDATA on Windows), applying per-OS patches.
resource: tagui.py#L318
tags: [rpa, tagui, installation]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.setup

# Responsibility

Installs TagUI so [`init()`](init.md) can launch it. Called automatically
by `init()` the first time it can't find the TagUI executable, or can be
run standalone. Downloads the OS-specific TagUI zip (or unpacks an
existing local `rpa_python.zip` produced by [`pack()`](pack.md) for
offline installs), unzips it to `tagui_location()`, syncs the latest
"delta" files, and applies macOS-specific fixes (PhantomJS upgrade,
`python` → `python3` patch) and Linux permission/PHP checks.

# Public Interface

```python
setup()  # no arguments; returns True/False
```

# Dependencies

- [tagui.download](download.md) — fetches the TagUI zip and any patch
  assets (vcredist, PhantomJS) from GitHub-hosted mirrors.
- Uses `unzip()`, `_tagui_delta()`, `_patch_macos_pjs()`,
  `_patch_macos_py3()`, and `tagui_location()` from [tagui](../tagui.md).

# Used By

- [tagui.init](init.md) — invoked automatically when the TagUI executable
  is not found at the expected location.
- [tagui.pack](pack.md) — indirectly, via the temporary `init()`/`close()`
  cycle it performs before zipping the installation.

# Usage Example

```python
import rpa as r
r.setup()   # or just call r.init() directly, which triggers this on first run
```

# Notes

- Two installation pathways: primary (download `TagUI_<OS>.zip` from the
  internet) and secondary (unzip a local `rpa_python.zip`, the artifact
  produced by [`pack()`](pack.md), for air-gapped machines).
- On Linux, requires `php` to already be installed and printed install
  instructions if it can't detect it; on Windows, auto-downloads and runs
  the Visual C++ Redistributable (`vcredist_x86.exe`) if TagUI's bundled
  PHP fails to run.
