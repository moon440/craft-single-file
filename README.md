# Craft single-file builds

Three full desktop-class editors, each packed into **one offline HTML file**:

| App | Version | Download | Size | SHA-256 |
| --- | --- | --- | --- | --- |
| **WordCraft** — word processor | 0.18.1 | [WordCraft.html](https://github.com/moon440/wordcraft/releases/download/single-file-v0.18.1/WordCraft.html) | 77.8 MiB | `18b069ef070ef8026a85e171ba70783513860b7b8ea0f2a19697d5de61debb79` |
| **FilmCraft** — video editor | 0.20.1 | [FilmCraft.html](https://github.com/moon440/filmcraft/releases/download/single-file-v0.20.1/FilmCraft.html) | 63.9 MiB | `a54feb5b9f3973f58838eb3904053b33540156f670268acdbd2dec565db3131b` |
| **PhotoCraft** — photo editor | 0.23.1 | [PhotoCraft.html](https://github.com/moon440/photocraft/releases/download/single-file-v0.23.1/PhotoCraft.html) | 50.3 MiB | `d4f868780ab8faeefd8526d2496c7ce653f5e9aca437440ff3f74fc2f69abe10` |

Download one file, double-click it, and the app runs. **No web server, no install, no folder of
side files, no internet connection.** Everything the browser needs is inside the HTML: the
WebAssembly module, the wasm-bindgen JavaScript glue, the markup, the CSS, the icons, and the
licence texts.

## Why this exists

The official Craft web releases are folders that must be served over HTTP: an `index.html`, a
JavaScript glue file, a `.wasm` module, sometimes a boot loader or an audio worklet. Copy the
HTML alone and it breaks. That is fine for a website, but it is not an artifact you can keep on a
disk and open in ten years.

These builds repack the same, unmodified apps as a single HTML file that boots from `file://`.
A browser loads it, decodes the embedded wasm, and starts the app. Nothing is requested over the
network — the only URL the page touches is its own file.

## What "single file" covers

- **WebAssembly** module embedded as base64 and decoded to a `Uint8Array` at start-up.
- **wasm-bindgen glue** inlined into an inline `<script type="module">`.
- **Markup, CSS, canvas, icons** inline; icons are data URIs.
- **Audio worklet** (FilmCraft) served from an embedded `data:` URL, because Chrome blocks
  `blob:` worklets on `file://`.
- **PhotoCraft's gzipped app part** inflated at build time; its 102 KB boot loader is skipped.
- **NOTICE, MIT and Apache licences, ATTRIBUTION.md** embedded verbatim.

## Verified

- Boots from `file://` in Chrome 157 with no requests other than the HTML file itself.
- WordCraft: blank document opened and typed into.
- FilmCraft: demo project, timeline and preview render; worklet loads.
- PhotoCraft: home screen, canvas and GPU path initialise.
- `localStorage` and IndexedDB work from `file://` in Chrome, so projects and preferences save.

## Caveats

- These are WebAssembly + WebGL2/WebGPU apps. A browser without those cannot run them.
- The files are large because the wasm is embedded as base64 (about +33% over the raw module).
- Chrome treats every `file://` page as one storage area; saved data is keyed by each app, not by
  folder.
- `file://` behaviour differs slightly between engines. Chrome and Edge are the tested path;
  Safari should be checked locally.

## Source forks

Each build lives next to its upstream project as a fork, with the same licence:

- [moon440/wordcraft](https://github.com/moon440/wordcraft) — fork of
  [storytold/wordcraft](https://github.com/storytold/wordcraft)
- [moon440/filmcraft](https://github.com/moon440/filmcraft) — fork of
  [storytold/filmcraft](https://github.com/storytold/filmcraft)
- [moon440/photocraft](https://github.com/moon440/photocraft) — fork of
  [storytold/photocraft](https://github.com/storytold/photocraft)

Each fork has a `SINGLE-FILE-BUILD.md` with the exact changes and checksums, and the build as a
release asset.

## Credits

All three apps — the Rust code, the UI, the rendering, the codecs and the assets — are the work
of the **ArtCraft Team and the Craft contributors**, from
[storytold/wordcraft](https://github.com/storytold/wordcraft),
[storytold/filmcraft](https://github.com/storytold/filmcraft) and
[storytold/photocraft](https://github.com/storytold/photocraft). This project only repacks their
official web releases into single HTML files. It does not modify the apps. Credit them for the
software; credit this repo only for the packaging.

## Licence

The apps are **MIT OR Apache-2.0**, by the ArtCraft Team and the Craft contributors. This
repackaging keeps the same terms; see [LICENSE-MIT](LICENSE-MIT) and
[LICENSE-APACHE](LICENSE-APACHE).

The ArtCraft name, wordmark and marks are trademarks of the ArtCraft Team, are not open source,
and are not included in these builds. These are repacked official app builds, not ArtCraft
products.
