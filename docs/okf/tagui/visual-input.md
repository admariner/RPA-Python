---
type: Function
title: tagui.keyboard / tagui.mouse / tagui.vision
description: Send keyboard key combinations, mouse button down/up events, or raw SikuliX "vision" commands — all require visual automation mode.
resource: tagui.py#L1327
tags: [rpa, tagui, visual-automation]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.keyboard / tagui.mouse / tagui.vision

# Responsibility

Three thin dispatchers that all share the same guard: require `init()`,
require a non-empty argument, and specifically require
`init(visual_automation=True)` (checked via `_visual()`) — these
capabilities only work through SikuliX, TagUI's visual-automation engine,
not the default Chrome-only mode.

- `keyboard(keys_and_modifiers)`: sends `keyboard <keys>` — TagUI/SikuliX
  key-combination syntax (e.g. modifier+key sequences).
- `mouse(mouse_action)`: only accepts the literal strings `'down'` or
  `'up'` (case-insensitive); sends `mouse down`/`mouse up`.
- `vision(command_to_run)`: sends `vision <command>` verbatim — a direct
  escape hatch into SikuliX vision commands not otherwise exposed.

# Public Interface

```python
keyboard(keys_and_modifiers=None)   # returns True/False
mouse(mouse_action=None)            # 'down' or 'up'; returns True/False
vision(command_to_run=None)         # returns True/False
```

# Dependencies

- [tagui.send](send.md) — all three just forward to `send()`.
- Uses `_visual()`, `_esq()` from [tagui](../tagui.md).

# Used By

Standalone calls, only usable after `init(visual_automation=True)`.

# Usage Example

```python
r.init(visual_automation=True)
r.mouse('down'); r.mouse('up')
r.keyboard('[ctrl]a')
r.vision('click Submit.png')
```
