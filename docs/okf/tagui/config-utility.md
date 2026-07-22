---
type: Function
title: tagui.coord / debug / error / show_error / tagui_location / unzip / timeout / download_location
description: Small configuration and utility getters/setters used throughout the module — coordinate formatting, debug/error mode, install location, timeout, and download directory.
resource: tagui.py#L261
tags: [rpa, tagui, configuration]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.coord / debug / error / show_error / tagui_location / unzip / timeout / download_location

# Responsibility

Grouped because each is a small get-or-set-if-given accessor around one
piece of module state, with no TagUI process interaction of its own
(except `timeout()`, which also forwards to TagUI via `send()`):

- `coord(x, y)` — pure string formatting: `'(' + str(x) + ',' + str(y) + ')'`.
  The building block every coordinate-overloaded function
  ([click family](click-family.md), [type/select](type-select.md),
  [read/snap](read-snap.md), [exist/present](exist-present.md)) uses to
  turn integer arguments into the identifier string TagUI expects.
- `debug(on_off)` — get/set `_tagui_debug`; when on, [`_ready()`](live-mode-protocol.md)
  echoes raw TagUI subprocess output to stdout, and
  [`close()`](close.md) skips deleting the `.log`/`.txt` output files.
  Passing a non-int value instead sends it as a TagUI comment
  (`// <value>`) rather than toggling debug mode — an odd overload worth
  knowing about.
- `error(on_off)` — get/set `_tagui_error`; when True,
  [`show_error()`](#) raises `Exception` instead of just printing.
- `show_error(error_message)` — the module's single error-reporting
  choke point: prints or raises depending on `error()`, always returns
  `False` (so callers can `return show_error(...)`).
- `tagui_location(location)` — get/set the folder TagUI is/will be
  installed to (defaults to `%APPDATA%` on Windows, else `~`). Read by
  [`setup()`](setup.md)/[`init()`](init.md) to locate the executable.
- `unzip(file_to_unzip, unzip_location)` — thin `zipfile` wrapper used by
  [`setup()`](setup.md) and [setup internals](setup-internals.md).
- `timeout(timeout_in_seconds)` — get/set `_tagui_timeout` (default 10.0s,
  the poll ceiling [`exist()`](exist-present.md) uses inside TagUI's own
  JS); also forwards the new value to TagUI via `send('timeout ...')`
  when setting.
- `download_location(location)` — get/set the browser's download
  directory by sending a Chrome DevTools Protocol
  `Page.setDownloadBehavior` instruction; rejects locations containing a
  single quote (would break the raw JS string it builds).

# Public Interface

```python
coord(x_coordinate=0, y_coordinate=0)             # -> str "(x,y)"
debug(on_off=None)                                 # -> bool
error(on_off=None)                                 # -> bool
show_error(error_message=None)                     # -> False (side effect: print/raise)
tagui_location(location=None)                      # -> str
unzip(file_to_unzip=None, unzip_location=None)     # -> True/False
timeout(timeout_in_seconds=None)                   # -> float
download_location(location=None)                   # -> str or True/False
```

# Dependencies

- `timeout()`/`download_location()` use [tagui.send](send.md).
- `setup()` uses `unzip()`/`tagui_location()` directly (see
  [tagui.setup](setup.md)).

# Used By

Used pervasively by nearly every other concept in this module — see
individual mentions above.

# Usage Example

```python
r.timeout(30)                 # wait up to 30s for elements to exist
r.error(True)                 # raise exceptions instead of printing errors
r.tagui_location('/opt/tagui_home')
```
