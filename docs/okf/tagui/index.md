# tagui — Key Functions

Function-level concepts for the entry points in [tagui](../tagui.md) that
have non-trivial logic (process lifecycle, installation, offline
packaging). The rest of the module's public functions are thin one-line
wrappers around [`send()`](send.md) and are catalogued in the module's own
Public Interface table rather than documented individually.

* [setup](setup.md) - downloads and installs TagUI for the current OS.
* [init](init.md) - starts the TagUI subprocess and waits for it to become
  ready.
* [send](send.md) - sends one instruction to the running TagUI process and
  waits for it to finish.
* [close](close.md) - terminates the TagUI subprocess and cleans up
  generated files.
* [pack](pack.md) - bundles a TagUI installation for offline ("air-gapped")
  deployment.
* [update](update.md) - builds a self-extracting update script for an
  offline deployment.
* [download](download.md) - Python 2/3-compatible URL-to-file download
  helper used throughout the module.
