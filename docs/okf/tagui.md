---
type: Module
title: tagui
description: Python wrapper module that drives a TagUI subprocess in "live mode" to provide RPA for Python's public automation API.
resource: tagui.py
tags: [rpa, tagui, automation, python2-3-compat]
timestamp: 2026-07-22T09:58:59Z
---

# tagui

# Responsibility

`tagui.py` is the entire implementation of the `rpa`/`tagui` PyPI package.
It launches and manages a [TagUI](https://github.com/tebelorg/Tump)
subprocess in "live mode" (`init()`/`close()`), and exposes every RPA
capability (web/desktop automation, computer vision, OCR, keyboard/mouse
control, file I/O) as a flat set of module-level functions that translate
into TagUI instructions sent over the subprocess's stdin
([`send`](tagui/send.md)). It also contains TagUI's own installation,
update, and offline-packaging logic ([`setup`](tagui/setup.md),
[`update`](tagui/update.md), [`pack`](tagui/pack.md)), and Python 2/3
string/byte compatibility shims (`_py23_*` helpers) since the module
supports both.

`rpa_package/rpa.py` (see [rpa-package/rpa](rpa-package/rpa.md)) is an
identical copy of this file distributed as the `rpa` package name; `pack()`
regenerates it from `tagui.py` on every run.

# Public Interface

Process lifecycle, setup, and the wire protocol connecting them — see
[tagui/live-mode-protocol](tagui/live-mode-protocol.md) for how these fit
together, and [tagui/setup-internals](tagui/setup-internals.md) for the
private per-OS helpers they lean on:

```python
setup()                                                                       # tagui/setup.md
init(visual_automation=False, chrome_browser=True,
     headless_mode=False, turbo_mode=False)                                   # tagui/init.md
send(tagui_instruction=None)                                                  # tagui/send.md
close()                                                                       # tagui/close.md
pack()                                                                        # tagui/pack.md
update()                                                                      # tagui/update.md
download(download_url=None, filename_to_save=None)                           # tagui/download.md
```

Configuration / diagnostics — see
[tagui/config-utility](tagui/config-utility.md):

```python
coord(x_coordinate=0, y_coordinate=0)
debug(on_off=None)
error(on_off=None)
show_error(error_message=None)
tagui_location(location=None)
unzip(file_to_unzip=None, unzip_location=None)
timeout(timeout_in_seconds=None)
download_location(location=None)
```

Web / desktop UI interaction — each waits for readiness via `_started()`/
[`exist()`](tagui/exist-present.md) then delegates to
[`send()`](tagui/send.md):

```python
url(webpage_url=None)                                                        # tagui/url.md
exist(element_identifier=None) / present(element_identifier=None)            # tagui/exist-present.md
click(...) / rclick(...) / dclick(...)                                       # tagui/click-family.md
hover(element_identifier=None, test_coordinate=None)                         # tagui/hover.md
type(...) / select(...)                                                      # tagui/type-select.md
read(...) / snap(...)                                                        # tagui/read-snap.md
table(...) / upload(...) / bin(...)                                          # tagui/table-upload-bin.md
frame(main_frame=None, sub_frame=None) / popup(string_in_url=None)           # tagui/frame-popup.md
count(element_identifier=None) / title() / text() / timer()                  # tagui/state-readers.md
dom(statement_to_run=None) / run(command_to_run=None)                        # tagui/dom-run.md
api(url_to_query=None)  # stub; recommends using the `requests` package instead
```

Keyboard / mouse / vision (require `init(visual_automation=True)`) — see
[tagui/visual-input](tagui/visual-input.md),
[tagui/mouse-position](tagui/mouse-position.md),
[tagui/clipboard](tagui/clipboard.md):

```python
keyboard(keys_and_modifiers=None) / mouse(mouse_action=None) / vision(command_to_run=None)
mouse_xy() / mouse_x() / mouse_y()
clipboard(text_to_put=None)
```

Misc — see [tagui/misc](tagui/misc.md) and
[tagui/string-utility](tagui/string-utility.md):

```python
wait(delay_in_seconds=5.0)
check(condition_to_check=None, text_if_true='', text_if_false='')
ask(text_to_prompt='')
focus(app_to_focus=None)
telegram(telegram_id=None, text_to_send=None, custom_endpoint=None)  # deprecated
get_text(source_text=None, left=None, right=None, count=1)
del_chars(source_text=None, characters=None)
```

Trivial file I/O one-liners (no dedicated concept — genuinely simple
wrappers around Python's own `open()`/`print`):

```python
load(filename_to_load=None)                      # read a file's contents
echo(text_to_echo='')                            # print()
dump(text_to_dump=None, filename_to_save=None)   # overwrite file with text
write(text_to_write=None, filename_to_save=None) # append text to file
```

# Dependencies

None outside the standard library — imports only `subprocess`, `os`,
`sys`, `time`, `platform` (plus `zipfile`, `shutil`, `base64`, `urllib`
locally inside specific functions).

# Used By

- [rpa-package/rpa](rpa-package/rpa.md) — re-exports everything from this
  module (`from tagui import *`) under the `rpa` package name.
- [sample](sample.md) — exercises the public API (`init`, `url`, `type`,
  `read`, `click`, `wait`, `snap`, `close`).
- [setup](setup.md) — packages this file as the `tagui` PyPI distribution
  (`py_modules=['tagui']`).

# Usage Example

```python
import rpa as r
r.init()
r.url('https://ca.yahoo.com')
r.type('ybar-sbq', 'github')
r.click('ybar-search')
r.snap('page', 'results.png')
r.close()
```

# Notes

- Supports both Python 2 and Python 3 via the `_python2_env()`/
  `_python3_env()`/`_py23_*` helpers — string/byte handling is duplicated
  throughout for this reason.
- `telegram()` and `bin()` are marked deprecated in the project README
  (rely on a third-party `tebel.org` relay service); kept for backward
  compatibility but not recommended for new code.
- Nearly every function first checks `_started()` (must call `init()`
  first) and, for visual/chrome-only calls, `_visual()`/`_chrome()` before
  delegating to [`send()`](tagui/send.md); this repeated guard is the
  module's main structural pattern.
