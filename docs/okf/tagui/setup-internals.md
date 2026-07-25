---
type: Function
title: TagUI private setup/patch helpers
description: Private per-OS helpers — _tagui_delta, _patch_macos_pjs, _patch_macos_py3, _tagui_local, _python_flow/_visual_flow — that setup(), init(), and pack() all depend on.
resource: tagui.py#L182
tags: [rpa, tagui, installation, macos, internal]
timestamp: 2026-07-22T09:58:59Z
---

# TagUI private setup/patch helpers

# Responsibility

Private (`_`-prefixed) functions that encode the module's per-OS
installation and patching policy. Not part of the public API, but
non-trivial enough — and shared across multiple public entry points — to
warrant one dedicated concept rather than being buried inside
[setup](setup.md)/[init](init.md).

- `_python_flow()` / `_visual_flow()` — write the `rpa_python` "flow"
  file TagUI is launched with, selecting non-visual vs. visual-automation
  entry flow (the latter adds a dummy `mouse_xy()` trigger line to force
  SikuliX/Jython integration to initialize).
- `_tagui_local()` — writes `tagui_local.js`, defining two custom JS
  helpers TagUI loads at startup: a polling `exist(element_identifier)`
  that waits up to `casper.options.waitTimeout` before giving up (the
  actual implementation behind [`exist()`](exist-present.md)'s wait
  behavior), and an `add_concat()` shim needed because this module's
  Python environment can't use TagUI's native string-templating.
- `_tagui_delta(base_directory)` — downloads a fixed list of "delta"
  files (`tagui`, `tagui.cmd`, `end_processes`, `end_processes.cmd`,
  `tagui_header.js`, `tagui_parse.php`, `tagui.sikuli/tagui.py`) from a
  GitHub-hosted mirror that tracks TagUI's cutting-edge branch, to keep
  a stable-but-current TagUI build in sync with this package's release.
  Skips re-downloading if a marker file
  (`rpa_python_<__version__>`) for the current version already exists.
- `_patch_macos_pjs()` — one-time patch replacing TagUI's bundled
  PhantomJS with v2.1.1 on macOS, to work around an OpenSSL
  incompatibility with newer macOS versions; renames the old copy to
  `phantomjs_old` rather than deleting it.
- `_patch_macos_py3()` — one-time patch on macOS machines that only have
  `python3` (no plain `python` command): rewrites the shebang line
  (`#!/usr/bin/env python` → `#!/usr/bin/env python3`) in TagUI's bundled
  `casperjs`/`slimerjs` helper scripts.

# Public Interface

```python
_python_flow() / _visual_flow()        # write rpa_python flow file
_tagui_local()                         # write tagui_local.js
_tagui_delta(base_directory=None)      # returns True/False
_patch_macos_pjs()                     # returns True/False
_patch_macos_py3()                     # returns True (best-effort)
```

# Dependencies

- [tagui.download](download.md) — `_tagui_delta()`/`_patch_macos_pjs()`
  fetch files from GitHub-hosted mirrors.
- Uses `unzip()`, `load()`/`dump()`, `tagui_location()` from
  [tagui.config-utility](config-utility.md) / [tagui](../tagui.md).

# Used By

- [tagui.setup](setup.md) — calls `_tagui_delta()` after installing, and
  `_patch_macos_pjs()`/`_patch_macos_py3()` on macOS.
- [tagui.init](init.md) — calls all five: the flow-file writer matching
  `visual_automation`, `_tagui_local()`, `_tagui_delta()`, and the two
  macOS patches, on every start.

# Notes

- All three "patch"/"delta" functions are intentionally idempotent via a
  marker file (`rpa_python_<version>` or `phantomjs_old`/`py3_patched`
  directory checks) — safe to call on every `init()` without redoing
  work once applied for the current version.
