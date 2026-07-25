---
type: Function
title: tagui.send
description: Sends one TagUI live-mode instruction over the subprocess's stdin and blocks until TagUI reports it is ready for the next one.
resource: tagui.py#L819
tags: [rpa, tagui, process-lifecycle]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.send

# Responsibility

The single choke point almost every UI-interaction function in
[tagui](../tagui.md) funnels through. Escapes control characters in the
instruction string, writes it to the TagUI process's stdin wrapped in echo
markers (`[RPA][<id>] - ...` / `... - listening for inputs`), then loops
calling [`_ready()`](../tagui.md) until TagUI's stdout confirms it consumed
the instruction and is listening again.

# Public Interface

```python
send(tagui_instruction=None)  # returns True/False
```

# Dependencies

- Uses `_tagui_write()`/`_tagui_read()` (subprocess stdin/stdout) and
  `_ready()` from [tagui](../tagui.md).

# Used By

- Nearly every UI-interaction function in [tagui](../tagui.md) (`click`,
  `type`, `select`, `frame`, `popup`, `dom`, `keyboard`, `mouse`,
  `vision`, `timeout`, and others) builds a TagUI command string and
  passes it to `send()`.

# Usage Example

```python
# internal usage pattern, e.g. inside click():
send('click ' + _sdq(element_identifier))
```

# Notes

- Requires `init()` to have been called (`_tagui_started` True); errors
  via `show_error()` otherwise.
- Escapes `\`, `\n`, `\r`, `\t`, `\a`, `\b`, `\f` and a special
  `[BACKSLASH_QUOTE]` marker (used by [`_esq()`](../tagui.md) for embedded
  single quotes) before writing to the subprocess.
- If the TagUI process has already died (`_process.poll() is not None`),
  resets `_tagui_started`/`_tagui_visual`/`_tagui_chrome` to False and
  errors instead of writing to a dead pipe.
