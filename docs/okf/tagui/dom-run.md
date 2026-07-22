---
type: Function
title: tagui.dom / tagui.run
description: Execute an arbitrary JavaScript/DOM statement in the browser (dom), or an arbitrary shell command on the host (run), returning the result.
resource: tagui.py#L1618
tags: [rpa, tagui, escape-hatch]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.dom / tagui.run

# Responsibility

Both are deliberate escape hatches for anything the rest of the API
doesn't cover directly.

- `dom(statement_to_run)`: requires `init(chrome_browser=True)`. Sends
  `dom <statement>` to TagUI (which evaluates it in the page's JS
  context and stores the result in TagUI's `dom_result` variable), then
  dumps and reads that result back via
  `dump dom_result to rpa_python.txt`.
- `run(command_to_run)`: does not require `init()` at all — it's a plain
  `subprocess.check_output` wrapper. Appends `; exit 0` (or `& exit 0` on
  Windows) to the command so a non-zero exit code from the command
  itself doesn't raise `CalledProcessError`, and returns combined
  stdout+stderr.

# Public Interface

```python
dom(statement_to_run=None)      # returns the JS result as a string
run(command_to_run=None)        # returns combined stdout/stderr as a string
```

# Dependencies

- `dom()` uses [tagui.send](send.md) and `_tagui_output()`.
- `run()` uses only the standard library `subprocess` module — no
  dependency on the TagUI subprocess or `init()`.

# Used By

Standalone escape-hatch calls; not depended on by other documented
functions in this module.

# Usage Example

```python
page_height = r.dom('return document.body.scrollHeight')
output = r.run('ls -la')
```

# Notes

- `run()` executes with `shell=True`, so any untrusted input passed as
  `command_to_run` is a shell-injection risk — this is inherent to the
  function's purpose (arbitrary shell execution) rather than a bug, but
  callers building `command_to_run` from external input should sanitize
  it themselves.
