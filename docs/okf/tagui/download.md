---
type: Function
title: tagui.download
description: Python 2/3-compatible helper that downloads a file from a URL to a local path, used throughout the module for fetching TagUI assets.
resource: tagui.py#L1498
tags: [rpa, tagui, networking]
timestamp: 2026-07-22T09:58:59Z
---

# tagui.download

# Responsibility

Shared low-level download primitive. Wraps `urllib.urlretrieve` (Python 2)
or `urllib.request.urlretrieve` (Python 3) behind one signature, defaults
the save filename to the URL's last path segment, deletes any
pre-existing file at the destination first, and reports success purely by
checking the file exists afterward.

# Public Interface

```python
download(download_url=None, filename_to_save=None)  # returns True/False
```

# Dependencies

None beyond the standard library (`urllib`/`urllib.request`,
`os`) and `_python2_env()` from [tagui](../tagui.md).

# Used By

- [tagui.setup](setup.md) — downloads the TagUI zip and (on macOS/Windows)
  patch dependencies (PhantomJS, `vcredist_x86.exe`).
- [tagui.pack](pack.md) — downloads the SikuliX Jython jar and the
  Windows Visual C++ Redistributable.
- [tagui.update](update.md) — downloads the latest `tagui.py` and each
  TagUI delta file.
- `focus()` — downloads `sendKeys.bat` on Windows if not already present.

# Usage Example

```python
# internal usage pattern:
download('https://github.com/tebelorg/Tump/releases/download/v1.0.0/TagUI_Linux.zip',
         home_directory + '/TagUI_Linux.zip')
```

# Notes

- Swallows the underlying exception and converts any failure (bad URL, no
  internet, no content) into a `False` return plus a printed error via
  `show_error()`, rather than propagating the original exception.
