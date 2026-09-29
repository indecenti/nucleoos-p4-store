# nucleoos-p4-store

App store and firmware updates for [NucleoOS-P4](https://github.com/indecenti/NucleoOS-P4), the
custom OS for the Guition JC1060P420C (ESP32-P4, 7" touchscreen).

Everything here is static, served by GitHub Pages at
**https://indecenti.github.io/nucleoos-p4-store/**. There is no server: the device only makes
HTTPS GET requests.

| What | URL | Used by |
|---|---|---|
| App store | `https://indecenti.github.io/nucleoos-p4-store` | Settings → Update → App store (default from 1.1.106) |
| Firmware | `https://indecenti.github.io/nucleoos-p4-store/ota/manifest.json` | Settings → Update, and the check at every boot (default from 1.1.106) |
| Catalog in a browser | [index.html](https://indecenti.github.io/nucleoos-p4-store/) | people |

## Layout

```
store-<lang>.json    catalog in one language (en it es fr de), what the device asks for first
store.json           the English catalog, for firmware older than 1.1.106
apps/<id>/           manifest.json, app.wasm, and when present app.aot, icon.z, icon.argb
ota/manifest.json    {"version", "url", "notes", "size", "sha256"} of the current firmware
index*.html          the catalog as a web page
CREDITS.md           author, license and source of every app
```

Firmware images are not committed. Each one is attached to a
[release](https://github.com/indecenti/nucleoos-p4-store/releases) (`v<version>`, asset
`nucleos-anima.bin`), and the Pages workflow copies the one named by `ota/manifest.json` into
`ota/`, after checking its SHA-256. So the device downloads it straight from Pages, and the git
history stays small.

## How it is published

Nothing is edited here by hand. From a NucleoOS-P4 checkout:

```
python tools/dist.py store                 # rebuild the catalog + app files, commit, push
python tools/dist.py firmware              # build/nucleos-anima.bin -> release + ota/manifest.json
python tools/dist.py status                # what Pages is serving right now
```

The OTA release script (`release.ps1`) calls `dist.py firmware` itself. Pages goes live about a
minute after the push.

The local servers (`server/appstore/appstore_server.py` on :8090, the OTA folder on :8080) still
work and serve the same layout: point Settings at them to test without publishing.

## Licenses

- **WASM-4 carts** (the `wasm4` category): the authors' games from [wasm4.org](https://wasm4.org),
  under CC BY-NC-SA 4.0, see [LICENSE-carts.txt](LICENSE-carts.txt). Each cart's author and
  source page are in its `manifest.json` and in [CREDITS.md](CREDITS.md). The `app.aot` next to a
  cart is that cart compiled ahead of time for the ESP32-P4, shared under the same license.
  **Non-commercial use only.**
- **NucleoOS apps and firmware**: same license as
  [NucleoOS-P4](https://github.com/indecenti/NucleoOS-P4) (PolyForm Noncommercial 1.0.0). Apps
  ported from other projects (Lua, QuickJS, SQLite, …) keep their upstream license, noted in
  their `manifest.json`.
