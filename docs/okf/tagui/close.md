---
type: Function
title: tagui.close
description: Sends the 'done' instruction to terminate the TagUI live-mode subprocess and removes generated flow/log/temp files.
resource: tagui.py#L882
tags: [rpa, tagui, process-lifecycle]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.close

# Responsibility

The counterpart to [`init()`](init.md): tells TagUI to quit (`done`
instruction), waits for the subprocess to exit, then deletes the
generated `rpa_python` flow file, `rpa_python.js`, `rpa_python.raw`, and
`tagui_local.js` (from both the current directory and the directory
`init()` was originally called from, to handle scripts that `chdir()` in
between). Also removes `rpa_python.log`/`rpa_python.txt` unless
`debug()` mode is on.

# Public Interface

```python
close()  # returns True/False
```

# Dependencies

- Uses `_tagui_write()` and `debug()` from [tagui](../tagui.md).

# Used By

- [sample](../sample.md) — calls `r.close()` at the end of the script.
- [tagui.pack](pack.md) — closes the temporary `init()` session it opens
  internally before zipping the TagUI installation.

# Usage Example

```python
import rpa as r
r.init()
# ... automation steps ...
r.close()
```

# Notes

- Errors via `show_error()` if `init()` was never called, or if the TagUI
  process had already died unexpectedly (`_process.poll()` not None).
- Safe to call defensively — the module docs/README recommend "if you
  forget to close, just close() next time."
