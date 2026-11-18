# Maintaining the PDF viewer

This guide covers development and testing of the native PDF viewer integrated
into Zed. The current integration is based on upstream Zed `v1.28.1`.

For implementation details, see [ARCHITECTURE.md](ARCHITECTURE.md).

## Repository layout

- `crates/pdf_viewer`: Zed integration, GPUI views, rendering, search, thumbnails and SyncTeX.
- `crates/kkpdf_zed`: PDFium-backed rendering engine and dedicated worker.
- `crates/zed`: registration and toolbar integration.
- `assets/keymaps`: PDF viewer keyboard shortcuts.

Keep PDF-specific implementation in the two PDF crates where possible.

## Requirements

- Rust toolchain compatible with the workspace rust-toolchain file.
- Native PDFium shared library for the host platform.
- Normal system dependencies required to build Zed.

The loader supports PDFIUM_LIB_PATH (a library file) and PDFIUM_LIB_DIR (a directory).

## Install PDFium

Run the checksum-verifying downloader:

```bash
bash crates/kkpdf_zed/scripts/download_pdfium.sh
```

By default, it installs under ~/.local/share/kkpdf-zed/lib.
To choose another directory:

```bash
bash crates/kkpdf_zed/scripts/download_pdfium.sh /path/to/pdfium
```

Or point the loader at an existing installation:

```bash
export PDFIUM_LIB_DIR=/path/to/pdfium
# Or:
export PDFIUM_LIB_PATH=/path/to/libpdfium.so
```

Library names are libpdfium.so on Linux, libpdfium.dylib on macOS, and pdfium.dll on Windows.

## Build

From the repository root:

```bash
cargo check -p pdf_viewer --lib
cargo build -p zed
```

## Tests and formatting

Useful test commands:

```bash
cargo test -p kkpdf-zed
cargo test -p pdf_viewer --lib
cargo fmt --check
```

Tests requiring PDFium need the native library installed and discoverable at runtime.

## Manual verification

After changing viewer behaviour, build Zed and open a representative PDF.
Check the affected behaviour in the actual UI, not only through compilation.

Useful scenarios include:
- Opening PDFs from the project panel, file finder and command line.
- Scrolling, zooming and changing page layout.
- Thumbnail navigation and active-page tracking.
- Searching, cycling through matches and handling no matches.
- Reloading a PDF after its contents change on disk.
- Forward and inverse SyncTeX with a LaTeX document.

## Updating to a newer Zed release

This integration is based on upstream Zed v1.28.1. Updating it requires reviewing the integration points in ARCHITECTURE.md, adapting to changed Zed APIs, and rebuilding and testing the viewer.

Pay particular attention to workspace dependencies, project-item registration, pane toolbar setup, keymaps and remote project handling.

## Licensing and attribution

The pdf_viewer crate is licensed under GPL-3.0-or-later.
The vendored kkpdf_zed crate retains its upstream Apache-2.0 OR GPL-3.0-or-later licensing and attribution.
See crates/kkpdf_zed/NOTICE.md and the licence files in both crates.
