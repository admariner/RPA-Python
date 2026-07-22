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

Process lifecycle and setup (see individual concepts for the ones with
non-trivial logic):

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

Configuration / diagnostics:

```python
debug(on_off=None)                    # get/set debug mode (echoes TagUI output to stdout)
error(on_off=None)                    # get/set whether errors raise Exception instead of just printing
show_error(error_message=None)        # print or raise the given error message, per error() mode
tagui_location(location=None)         # get/set the folder TagUI is installed to (default: user home)
timeout(timeout_in_seconds=None)      # get/set the default wait timeout (seconds) for UI elements
download_location(location=None)      # get/set the browser's file download directory
coord(x_coordinate=0, y_coordinate=0) # build an "(x,y)" coordinate string accepted by other calls
```

Web / desktop UI interaction (each waits for readiness via `_started()`/
`exist()` then delegates to `send()`):

```python
url(webpage_url=None)                                    # navigate, or return current URL
exist(element_identifier=None)                            # True if element/coordinate exists (waits up to timeout)
present(element_identifier=None)                          # True if element/coordinate is present now (no wait)
click(element_identifier=None, test_coordinate=None)
rclick(element_identifier=None, test_coordinate=None)      # right-click
dclick(element_identifier=None, test_coordinate=None)      # double-click
hover(element_identifier=None, test_coordinate=None)
type(element_identifier=None, text=None, test_coordinate=None)
select(element_identifier=None, option_value=None, test_coordinate=None)
read(element_identifier=None, test_coordinate=None)
snap(element_identifier=None, filename_to_save=None, test_coordinate=None)
table(element_identifier=None, filename_to_save=None)      # export an HTML table to CSV
upload(element_identifier=None, filename_to_upload=None)
frame(main_frame=None, sub_frame=None)                     # switch webpage context into an iframe
popup(string_in_url=None)                                  # switch webpage context into a popup/tab
count(element_identifier=None)
title()
text()
dom(statement_to_run=None)                                 # run and return an arbitrary JS/DOM statement (chrome mode)
api(url_to_query=None)                                     # stub; recommends using the `requests` package instead
```

Keyboard / mouse / vision (require `init(visual_automation=True)`):

```python
keyboard(keys_and_modifiers=None)
mouse(mouse_action=None)               # 'down' or 'up'
vision(command_to_run=None)
mouse_xy() / mouse_x() / mouse_y()
clipboard(text_to_put=None)            # get/set OS clipboard
```

Misc utilities:

```python
wait(delay_in_seconds=5.0)
check(condition_to_check=None, text_if_true='', text_if_false='')
ask(text_to_prompt='')                 # JS prompt() in chrome mode, console input otherwise
focus(app_to_focus=None)               # bring a desktop app window to the foreground (Windows/macOS only)
run(command_to_run=None)               # run a shell command, return its output
timer()                                # elapsed seconds of current TagUI flow
telegram(telegram_id=None, text_to_send=None, custom_endpoint=None)  # deprecated, see Notes
bin(file_to_bin=None, password=None, server='https://tebel.org/bin/')  # deprecated, see Notes
unzip(file_to_unzip=None, unzip_location=None)
load(filename_to_load=None)
echo(text_to_echo='')
dump(text_to_dump=None, filename_to_save=None)   # overwrite file with text
write(text_to_write=None, filename_to_save=None) # append text to file
get_text(source_text=None, left=None, right=None, count=1)  # substring between two delimiters
del_chars(source_text=None, characters=None)                # strip given characters from a string
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
