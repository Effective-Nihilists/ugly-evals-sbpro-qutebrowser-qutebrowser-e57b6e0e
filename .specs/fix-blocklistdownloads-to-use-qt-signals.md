# Fix BlocklistDownloads to use Qt signals

## Context
The `BlocklistDownloads` class in `qutebrowser/components/utils/blockutils.py` uses callback functions passed to its constructor. The TICKET demands it be converted to use Qt signals.

## Plan
- [ ] Make `BlocklistDownloads` a `QObject` subclass with `single_download_finished` and `all_downloads_finished` `pyqtSignal`s
- [ ] Keep backward-compatible constructor (accepts callbacks), wire callbacks internally via signals
- [ ] Remove `finally: download.fileobj.close()` from `_on_download_finished` to fix ResourceWarning from unclosed `TextIOWrapper` in the test
- [ ] Run failing test to verify passes

## Verification
- `pytest tests/unit/components/test_blockutils.py::test_blocklist_dl -v` passes with 0 failures
