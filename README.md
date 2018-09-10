# SIMDebian (experimental)

Project status: [still doing initialization works](https://github.com/SIMDebian/SIMDebian/issues/1)

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

#### SIMDebian Archive

Currently there is only source repos. No pre-built binary packages.

## Benchmakrs

To proof this project is useful we need some benchmark data.

## Core Packages

#### Development

* Dpkg https://github.com/SIMDebian/dpkg/tree/simd

We forked dpkg to add SIMD buildflag support. With the patched dpkg, one just need to rebuild the debian
package with the following change:

```diff
-export DEB_BUILD_MAINT_OPTIONS  = hardening=+all
+export DEB_BUILD_MAINT_OPTIONS  = hardening=+all simd=+haswell
```
