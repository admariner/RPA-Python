# tagui — Function Concepts

Function-level concepts for [tagui](../tagui.md), grouped by shared
implementation pattern where several functions have (near-)identical
bodies. Only `load`/`echo`/`dump`/`write` are left undocumented
individually — genuinely trivial one-line file/print wrappers, catalogued
in the module's own Public Interface table instead.

Process lifecycle, installation, and offline packaging:

* [live-mode-protocol](live-mode-protocol.md) - the echo-marker
  instruction protocol connecting `init`/`send`/`close`.
* [setup](setup.md) - downloads and installs TagUI for the current OS.
* [setup-internals](setup-internals.md) - the private per-OS helpers
  `setup()`/`init()`/`pack()` all depend on.
* [init](init.md) - starts the TagUI subprocess and waits for it to
  become ready.
* [send](send.md) - sends one instruction to the running TagUI process.
* [close](close.md) - terminates the TagUI subprocess and cleans up.
* [pack](pack.md) - bundles a TagUI installation for offline deployment.
* [update](update.md) - builds a self-extracting update script for an
  offline deployment.
* [download](download.md) - Python 2/3-compatible URL-to-file download
  helper used throughout the module.

Web / desktop UI interaction:

* [exist-present](exist-present.md) - `exist()`/`present()`, the
  existence checks nearly everything else guards on.
* [click-family](click-family.md) - `click()`/`rclick()`/`dclick()`.
* [hover](hover.md) - `hover()`.
* [type-select](type-select.md) - `type()`/`select()`.
* [read-snap](read-snap.md) - `read()`/`snap()`.
* [table-upload-bin](table-upload-bin.md) - `table()`/`upload()`/`bin()`
  (`bin()` deprecated).
* [url](url.md) - `url()`, navigate or query current URL.
* [frame-popup](frame-popup.md) - `frame()`/`popup()`, iframe/tab context
  switching.
* [state-readers](state-readers.md) - `count()`/`title()`/`text()`/`timer()`.
* [dom-run](dom-run.md) - `dom()`/`run()`, JS/shell escape hatches.

Visual automation (`init(visual_automation=True)`):

* [visual-input](visual-input.md) - `keyboard()`/`mouse()`/`vision()`.
* [mouse-position](mouse-position.md) - `mouse_xy()`/`mouse_x()`/`mouse_y()`.
* [clipboard](clipboard.md) - `clipboard()`.

Misc / utility:

* [config-utility](config-utility.md) - `coord()`, `debug()`, `error()`,
  `show_error()`, `tagui_location()`, `unzip()`, `timeout()`,
  `download_location()`.
* [misc](misc.md) - `ask()`, `check()`, `wait()`, `focus()`, `telegram()`
  (deprecated).
* [string-utility](string-utility.md) - `get_text()`/`del_chars()`.
