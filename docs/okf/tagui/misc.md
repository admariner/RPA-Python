---
type: Function
title: tagui.ask / tagui.check / tagui.wait / tagui.focus / tagui.telegram
description: Standalone helpers with no shared implementation pattern — user prompt, conditional print, sleep, desktop app focus, and a deprecated Telegram notifier.
resource: tagui.py#L1277
tags: [rpa, tagui, misc, deprecated]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.ask / tagui.check / tagui.wait / tagui.focus / tagui.telegram

# Responsibility

Five functions grouped only because each is a standalone one-off, not
because they share logic:

- `ask(text_to_prompt)` — in `init(chrome_browser=True)` mode, shows a
  JS `prompt()` dialog via [`dom()`](dom-run.md) and returns what the
  user typed; otherwise falls back to a plain console
  `input()`/`raw_input()` (Python 2/3).
- `check(condition_to_check, text_if_true, text_if_false)` — prints one
  of two messages depending on a boolean and always returns True; a
  scripting convenience, no TagUI interaction.
- `wait(delay_in_seconds=5.0)` — `time.sleep()` wrapper.
- `focus(app_to_focus)` — brings a desktop application window to the
  foreground. Windows: downloads and runs a `sendKeys.bat` helper (from
  a GitHub-hosted mirror, downloaded once, then reused) via `os.system`.
  macOS: runs `osascript -e 'tell application "..." to activate'`. Linux
  is explicitly unsupported (returns False with an error).
- `telegram(telegram_id, text_to_send, custom_endpoint)` — **deprecated**
  (per the project README). Sends a message via a hardcoded relay at
  `https://tebel.org/rpapybot/sendMessage.php` (or `custom_endpoint`)
  using `urllib`; swallows all exceptions (no internet, bad ID, relay
  down) and returns False rather than raising.

# Public Interface

```python
ask(text_to_prompt='')                                     # -> str
check(condition_to_check=None, text_if_true='', text_if_false='')  # -> True
wait(delay_in_seconds=5.0)                                  # -> True
focus(app_to_focus=None)                                    # -> True/False, Windows/macOS only
telegram(telegram_id=None, text_to_send=None, custom_endpoint=None)  # -> True/False
```

# Dependencies

- `ask()` depends on [tagui.dom-run](dom-run.md) (`dom()`) and `_chrome()`.
- `focus()` depends on [tagui.download](download.md) on Windows
  (`sendKeys.bat`).

# Used By

Standalone calls; not depended on by other documented functions.

# Usage Example

```python
name = r.ask('What is your name?')
r.check(name != '', 'got a name', 'no name given')
r.wait(2)
r.focus('Notepad')
```

# Notes

- `telegram()` is explicitly deprecated in the README (relies on a
  third-party relay service) — avoid it in new code, same caveat as
  [`bin()`](table-upload-bin.md).
