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
