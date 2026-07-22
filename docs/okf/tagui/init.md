---
type: Function
title: tagui.init
description: Starts (or installs then starts) the TagUI subprocess in live mode and blocks until it is ready to receive instructions.
resource: tagui.py#L471
tags: [rpa, tagui, process-lifecycle]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.init

# Responsibility

Entry point every automation script must call before any UI-interaction
function. Ensures TagUI is installed (calling [`setup()`](setup.md) if
not), writes a "flow" file selecting visual vs. non-visual automation,
launches the TagUI executable via `subprocess.Popen`, and busy-waits on
its stdout until it prints the live-mode ready marker, at which point it
flips `_tagui_started`/`_tagui_visual`/`_tagui_chrome` module state to
True so other functions will proceed.

# Public Interface

```python
init(visual_automation=False, chrome_browser=True,
     headless_mode=False, turbo_mode=False)  # returns True/False
```

# Dependencies

- [tagui.setup](setup.md) — invoked if the TagUI executable isn't present
  yet.
- Uses `_tagui_delta()`, `_patch_macos_pjs()`, `_patch_macos_py3()`,
  `_python_flow()`/`_visual_flow()`, `_tagui_local()`, `_tagui_read()`/
  `_tagui_write()`, and `_ready()` from [tagui](../tagui.md).

# Used By

- Every UI-interaction function in [tagui](../tagui.md) checks
  `_started()` and errors if `init()` hasn't been called.
- [tagui.pack](pack.md) — calls `init(False, False)` internally to force a
  first-run TagUI sync before zipping.
- [sample](../sample.md) — calls `r.init()` first thing.

# Usage Example

```python
import rpa as r
r.init()                                   # default: no visual automation, chrome browser on
r.init(visual_automation=True)             # enables keyboard()/mouse()/vision()/clipboard()
r.init(chrome_browser=False)               # no browser, e.g. for desktop-only automation
```

# Notes

- `visual_automation=True` additionally requires a working 64-bit
  OpenJDK/Java on the machine (checked via `java -version`); `init()`
  prints install guidance and returns False if missing.
- `turbo_mode` patches TagUI's own `tagui_chrome.php`/`tagui_header.js`/
  `tagui.sikuli/tagui.py` files in place to shrink scan/sleep intervals —
  calling `init()` again with a different `turbo_mode` value toggles the
  same patched files back and forth.
- Raises via [`show_error()`](../tagui.md) (print or exception, per
  `error()` mode) if called again without an intervening
  [`close()`](close.md).
