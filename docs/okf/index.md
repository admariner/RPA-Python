---
okf_version: "0.1"
source_repo: marcelocecin/RPA-Python
source_commit: 8259b4ca1005a79523e672c1c833accd84fccb76
generated_by: okf-repo-docs skill
---

# RPA for Python — OKF Documentation

RPA for Python is a Python wrapper around [TagUI](https://github.com/tebelorg/Tump),
an open-source RPA (robotic process automation) tool. It exposes website
automation, computer-vision automation, OCR, and keyboard/mouse automation
through a single flat function-based API (`import rpa as r`). The wrapper
manages a TagUI subprocess in "live mode": every call from Python is sent as
one instruction over the subprocess's stdin and the reply is read back from
a temp output file or stdout.

## Modules

* [tagui](tagui.md) - the wrapper module: process lifecycle, TagUI
  installation/update, and the full public automation API.

## Key functions

* [tagui/setup](tagui/setup.md) - downloads and installs TagUI for the
  current OS.
* [tagui/init](tagui/init.md) - starts the TagUI subprocess and waits for
  it to become ready.
* [tagui/send](tagui/send.md) - sends one instruction to the running TagUI
  process and waits for it to finish.
* [tagui/close](tagui/close.md) - terminates the TagUI subprocess and
  cleans up generated files.
* [tagui/pack](tagui/pack.md) - bundles a TagUI installation for offline
  ("air-gapped") deployment.
* [tagui/update](tagui/update.md) - builds a self-extracting update script
  for an offline deployment.
* [tagui/download](tagui/download.md) - Python 2/3-compatible URL-to-file
  download helper used throughout the module.

## Packaging

* [rpa-package](rpa-package/) - the distributable `rpa` PyPI package
  (`pip install rpa`), a thin re-export of `tagui.py` plus its own
  `setup.py`.
* [setup](setup.md) - root `setup.py`, packages this repo as the `tagui`
  PyPI distribution.
* [sample](sample.md) - `sample.py`, a runnable usage example exercising
  the public API end to end.
