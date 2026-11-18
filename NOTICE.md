# NOTICE

This repository (`spektrafilm-fnos`) contains **packaging and bridge code only**.
It does not fork upstream source, and it does not ship any upstream code as its
own. The film simulation it exposes comes from the upstream project at the
commit pinned in `UPSTREAM_PIN`:

- Upstream: https://github.com/andreavolpato/spektrafilm
- Pinned commit: see `UPSTREAM_PIN` (`main` at time of writing, v0.3.3)

## What this repository contains

| Path | What it is | License |
|---|---|---|
| `bridge/` | an HTTP/SSE front over the spektrafilm runtime: RPC, progress events, file API, upload inbox, fnOS trim-API + per-user ACL integration. Pure Python standard library. | GPL-3.0-or-later (this repo) |
| `frontend/` | the web UI (see `frontend/README.md`): its own React/Vite application, **not** a copy of any upstream GUI | GPL-3.0-or-later (this repo) |
| `fpk/`, `docker/`, `.github/`, `build.sh` | fnOS app packaging (fpk layout, lifecycle scripts, compose) and CI | GPL-3.0-or-later (this repo) |

## What is inherited from upstream

The runtime image installs the pinned upstream release (`pip install
spektrafilm==<version>`) into its own virtualenv. Its parts carry their own
licenses, reproduced verbatim in `licenses/`:

| Component | License | Source |
|---|---|---|
| spektrafilm code (`spektrafilm` runtime, `spektrafilm_lut_creator`) | GPL-3.0-or-later | `licenses/LICENSE` (upstream copy) |
| spektrafilm film/paper profiles, print LUTs, and their direct derivatives | CC BY-SA 4.0 | `licenses/SPEKTRAFILM_LICENSE.txt` (upstream copy) |
| third-party dependencies (numpy, scipy, colour-science, scikit-image, numba, pyfftw, rawpy, exiv2, PySide6, qtpy, napari, …) | their own licenses (BSD / MIT / Apache-2.0 / LGPL / PSF as applicable) | resolved by pip at build time; see `licenses/THIRD_PARTY.md` (generated) |

The pinned upstream `README.md` and `CITATION.cff` are kept in `licenses/` as
well, because the CITATION file records the citation and attribution requests
that apply to the runtime.

## Attribution

Per the upstream `CITATION.cff`, the runtime is described in publications and
credits as **spectral film modeling powered by `spektrafilm`**. That phrasing
appears in this repository's README and in the app's About surface. spektrafilm
is by Andrea Volpato (KTH Royal Institute of Technology); see
`licenses/CITATION.cff` for the full citation and DOI.

## What this project does not do

- It does **not** sell anything, and it does not gate upstream functionality behind payment.
- It does **not** redistribute the profiles or LUTs as a paid asset, and does not sell LUT packs.
- It does **not** train commercial AI models on the profiles, LUTs, or their outputs (upstream `SPEKTRAFILM_LICENSE.txt` asks for this).
- It does **not** pass itself off as official, upstream, or endorsed by spektrafilm or its author. It is an unofficial fnOS packaging and web front end.
- It does **not** remove or weaken the upstream licenses: `licenses/` ships them verbatim, and the GPL source correspondence obligation is met by `UPSTREAM_PIN` pointing at the exact upstream commit, plus the `licenses/` snapshot of its top-level files.

## GPL-3.0 source correspondence

Because this repository builds and distributes a derivative work (an image
containing the upstream runtime), the corresponding source is the upstream
checkout at `UPSTREAM_PIN`, obtainable from
https://github.com/andreavolpato/spektrafilm at that commit. `build.sh`
re-clones that exact commit, so the produced binaries are reproducible against
it.

## Unofficial status

`spektrafilm-fnos` is an independent, unofficial fnOS (飞牛 NAS) packaging
project. It is not affiliated with, endorsed by, or maintained by the spektrafilm
project or its author. Spektrafilm and any upstream marks remain their owner's
property.

---

*This file is a practical notice, not legal advice.*
