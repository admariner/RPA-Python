---
type: Module
title: TagUI live-mode instruction protocol
description: The stdin/stdout echo-marker protocol that init(), send(), close(), and _ready() use together to synchronize each instruction with the TagUI subprocess.
resource: tagui.py#L127
tags: [rpa, tagui, protocol, subprocess]
timestamp: 2026-07-22T09:58:59Z
---

# TagUI live-mode instruction protocol

# Responsibility

Explains the wire protocol underlying [`init()`](init.md),
[`send()`](send.md), [`close()`](close.md), and the private helpers they
share (`_tagui_read`, `_tagui_write`, `_ready`, `_tagui_output`) — none of
which is documented as a coherent whole in any single function's docs.

TagUI, once launched in "live mode" (`tagui rpa_python <browser_option>`),
reads instructions from its own stdin one line at a time and prints
output to stdout. This module layers a synchronous request/response
protocol on top of that raw pipe using **echo markers**:

1. Every instruction is assigned an incrementing integer ID
   (`_tagui_id`, reset to 0 in `init()`).
2. Before sending an instruction, `send()` writes an echo line:
   `echo "[RPA][<id>] - <escaped instruction text>"`, then the actual
   instruction, then a second echo line:
   `echo "[RPA][<id>] - listening for inputs"`.
3. `_ready()` reads TagUI's stdout line by line
   (`_tagui_read()` → `_process.stdout.readline()`) and returns True the
   moment it sees a line that starts with `[RPA][` and ends with
   `] - listening for inputs` — i.e. TagUI has finished echoing back the
   "listening" marker, proving the prior instruction fully executed.
4. `send()`/`init()` spin in a tight `while _tagui_started and not
   _ready(): pass` loop until that happens, then increment `_tagui_id`
   and return control to the caller.
5. For instructions that produce a value (e.g. `read()`, `url()` with no
   argument, `dom()`), the pattern is: send a TagUI expression that
   assigns to a TagUI-side variable, then
   `send('dump <variable> to rpa_python.txt')`, then call
   `_tagui_output()`, which polls for `rpa_python.txt` to appear (falling
   back to `_tagui_init_directory` if the caller `chdir()`'d away),
   reads and deletes it, and returns its contents.
6. `init()` itself uses the same `_ready()` loop, but waits for the
   *initial* `LIVE MODE - type done to quit` banner from TagUI before
   writing the first two echo lines (`[RPA][STARTED]` and
   `[RPA][0] - listening for inputs`) to prime the protocol.
7. `close()` ends the session by writing `echo "[RPA][FINISHED]"` then
   `done`, and simply waits for the subprocess to exit
   (`while _process.poll() is None: pass`) — it does not go through the
   `_ready()` echo-matching loop, since there's no further instruction to
   confirm.

All special characters (`\`, `\n`, `\r`, `\t`, `\a`, `\b`, `\f`) are
escaped by `send()` before writing, since the instruction is embedded
inside a shell `echo` argument; the `[BACKSLASH_QUOTE]` marker used by
[`_esq()`](../tagui.md) round-trips through this same escaping to
represent a literal `'` without breaking TagUI's own quoting rules.

# Public Interface

Not a standalone callable — this is the coordination pattern implemented
across:

```python
send(tagui_instruction=None)   # writer side of the protocol
_ready()                        # reader side: detects the "listening" marker
_tagui_read() / _tagui_write()  # raw subprocess.stdin/stdout access
_tagui_output()                 # polls/reads/deletes rpa_python.txt for return values
```

# Dependencies

None beyond the standard library (`subprocess`, `os`, `time`).

# Used By

- [tagui.init](init.md) — primes the protocol on subprocess startup.
- [tagui.send](send.md) — the primary writer.
- [tagui.close](close.md) — sends the terminal `done` instruction (bypasses
  the `_ready()` loop).
- Every value-returning function (`read`, `url`, `title`, `text`,
  `mouse_xy`, `dom`, ...) relies on the `dump ... to rpa_python.txt` +
  `_tagui_output()` half of this protocol.

# Notes

- The busy-wait loops (`while ... not _ready(): pass`,
  `while _process.poll() is None: pass`) spin the CPU rather than
  sleeping between polls — acceptable because TagUI responses are
  expected to be fast, but worth knowing if profiling shows unexpected
  CPU usage during automation.
- If the TagUI subprocess dies mid-instruction (`_process.poll()` becomes
  non-None), both `send()` and `_ready()` detect it and flip
  `_tagui_started`/`_tagui_visual`/`_tagui_chrome` back to False rather
  than hanging forever.
