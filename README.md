# SIMDebian (experimental)

## Project Background

In order to keep a good compatibility across different divices, Debian's ISA baseline is very low.
For example, the ISA baseline for amd64 (AKA x86_64) architecture is `<= SSE2` which means we
won't obtain any benifit from modern CPUs' SIMD instruction sets.

The baseline of SIMDebian has been bumped to:
* `amd64`: `SSE2` -> `AVX2`

## Project Workflow

The workflow is based on `git-buildpackage`. The three branchs from upstream (`master`, `upstream`, `pristine-tar`)
should not be modified. We make modifications on the `simd` branch, and append `+simd` to the debian revision number.
Alternatively, the `simd` mark can be replaced by specific ISA names e.g. `avx`, `avx2`, `neon`, etc. when that makes sense.

## Benchmakrs

To proof this project is useful we need some benchmark data.
