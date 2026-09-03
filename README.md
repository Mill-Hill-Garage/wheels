# wheels

Prebuilt Python wheels for Mill Hill Garage projects — CUDA builds of
open-source packages that PyPI doesn't ship in the configuration we need.
Consumed via `[tool.uv.sources]` URL pins (see each project's pyproject.toml).

## pycolmap-cuda12 (+caspar)

[pycolmap](https://github.com/colmap/colmap) built with `CASPAR_ENABLED` +
`CASPAR_USE_DOUBLE` (GPU bundle adjustment — the official PyPI wheels lack it)
and CUDA archs sm_80/86/89/90 (A100, Ampere, L4/RTX 4090, H100).
Measured 3.9x faster incremental mapping on a 2400-frame scan vs the official
wheel, identical registration.

Build recipe: `spatial-automation/scripts/build_pycolmap_caspar.sh`.
Runtime needs: glibc >= 2.35 (Ubuntu 22.04+), CUDA 12 runtime, libgomp,
libgfortran5, OpenGL libs (all present in nvidia/cuda images).

Licenses: COLMAP is BSD-3-Clause; Symforce-Caspar is Apache-2.0; vcpkg-built
dependencies retain their respective licenses.

## torch-scatter / torch-sparse (pt2.11.0 cu128)

Mirror of the two wheels we consumed from
`https://data.pyg.org/whl/torch-2.11.0+cu128.html`, which went down on
2026-09-03 (DNS failure, not HTTP) and broke every CI job and Docker build that
resolved them. uv reads every flat index in the resolution graph eagerly —
including a git dependency's — so the index had to go from both the
`spatial-automation` workspace root and the `SpatialLM` fork's `cu128` branch.

Not a rebuild: pyg was already unreachable, so these were recovered from a uv
cache that had synced them before the outage (the pristine unpack under
`~/.cache/uv/archive-v0/`) and repacked with `python -m wheel pack`. Every file
was verified against the `RECORD` that shipped inside the original wheel —
23/23 for torch-scatter, 57/57 for torch-sparse, zero mismatches — so the
contents are provably what pyg served and the filenames come out identical.

To recover a wheel this way from any machine that still has it installed, find
the package's unpack directory under `~/.cache/uv/archive-v0/` (it contains the
package dir plus its `.dist-info`) and run `python -m wheel pack <dir>`.

Runtime needs: CPython 3.12, torch 2.11.0+cu128, linux x86_64. `torch-sparse`
additionally needs `scipy`.

Licenses: torch-scatter and torch-sparse are MIT (Matthias Fey).
