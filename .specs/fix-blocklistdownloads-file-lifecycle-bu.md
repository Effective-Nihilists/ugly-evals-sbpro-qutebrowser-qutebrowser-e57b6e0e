# Fix BlocklistDownloads file lifecycle bug

## Context
`blockutils.BlocklistDownloads._on_download_finished` closes `download.fileobj` in a `finally` block after calling `_user_cb_single(download.fileobj)`. The test wraps the fileobj in `io.TextIOWrapper`; when blockutils closes the underlying file externally, the TextIOWrapper is left unclosed, triggering `ResourceWarning` → `PytestUnraisableExceptionWarning` → test failure.

## Repro
```
PYTHONPATH=$(pwd) uv run pytest tests/unit/components/test_blockutils.py::test_blocklist_dl -x -W ignore::DeprecationWarning
# → FAILED: ExceptionGroup: multiple unraisable exception warnings (10 sub-exceptions)
```

## Plan
- [ ] Remove `download.fileobj.close()` from `_on_download_finished` in `qutebrowser/components/utils/blockutils.py` — let the callback own the file lifecycle

## Verification
```
PYTHONPATH=$(pwd) uv run pytest tests/unit/components/test_blockutils.py::test_blocklist_dl -x -W ignore::DeprecationWarning
```
Should pass with 0 failures.
