---
type: Function
title: tagui.frame / tagui.popup
description: Switch the webpage's JS evaluation context into an iframe/sub-iframe, or into a popup/new-tab matching a URL substring.
resource: tagui.py#L1534
tags: [rpa, tagui, browser, chrome-devtools-protocol]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.frame / tagui.popup

# Responsibility

Both require `init(chrome_browser=True)` and work by sending raw
`js ...` TagUI instructions (via [`send()`](send.md)) that manipulate
global JS state (`chrome_context`, `chrome_targetid`) used internally by
TagUI's Chrome DevTools Protocol integration — there is no dedicated
TagUI command for either operation, so both hand-roll the JS.

- `frame(main_frame, sub_frame)`: always resets context to the document
  root first. With no arguments, that reset is all it does (returns
  True). With `main_frame`, it locates an iframe by `@name`/`@id` via an
  XPath existence check, computes its bounding rect (for later
  coordinate-based interaction), and repoints `chrome_context` at that
  iframe's `contentDocument`. `sub_frame`, if given, repeats the process
  one level deeper.
- `popup(string_in_url)`: always detaches any currently-attached popup
  target first. With no argument, that's all it does. With a URL
  substring, it lists all Chrome targets via the
  `Target.getTargets`/`Target.attachToTarget` CDP calls, finds the first
  target whose `url` contains the substring, and sets `chrome_targetid`
  to its new session ID so subsequent calls act on that tab/popup.

# Public Interface

```python
frame(main_frame=None, sub_frame=None)   # returns True/False
popup(string_in_url=None)                # returns True/False
```

# Dependencies

- [tagui.send](send.md) — every step is a raw `js ...` instruction.
- [tagui.exist-present](exist-present.md) — `frame()` uses `exist()` to
  confirm the target iframe is present before proceeding.

# Used By

Standalone browser-context calls; not depended on by other documented
functions in this module.

# Usage Example

```python
r.frame('payment-iframe')     # switch into an iframe by name/id
r.frame()                     # switch back to the main document
r.popup('checkout')           # switch into a popup/tab whose URL contains 'checkout'
r.popup()                     # detach from the current popup
```

# Notes

- Neither function validates that the sent JS actually succeeded beyond
  what's checked explicitly (`frame()` checks `exist()` for the iframe;
  `popup()` checks that `chrome_targetid` ended up non-empty) — malformed
  frame/tab state can otherwise pass silently.
