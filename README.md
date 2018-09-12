# SIMDebian (experimental)

Project status: [initialization](https://github.com/SIMDebian/SIMDebian/issues/1)

## Project Introduction

In order to keep a good compatibility across different divices, Debian's ISA
baseline is very low. For example, the ISA baseline for `amd64`
(a.k.a. `x86_64`) architecture is `<= SSE2` which means we won't benefit
from modern CPUs' SIMD instruction sets.

To recompile Debian package with SIMD code enabled, the simplest way is to
inject `-march=native` flag to the compiler flags and recompile the package.
However a big amount of repeated work has to be done in order to rebuild
the whole Debian system. Instead, SIMDebian chose to fork `dpkg` and
directly add the corresponding flag to system default flag list, such
that we can rebuild any package from Debian without change.

However, not every package would gain a performance boost after recompiling
with native machine code. Therefore SIMDebian only rebuild a set of
selected packages which would benefit from vectorized code to rebuild.

### ISA Baseline of SIMDebian

* `nehalem` for the first generation supporting SSE4.1
* (`sandybridge` for the first generation supporting AVX is missing)
* `haswell` for the first generation supporting AVX2
* `skylake` for my own cpu (hmm...)

## SIMDebian Archive

https://sim.debiancn.org/

Currently supported machine types: `nehalem`, `haswell`, `skylake`.

Example APT configuration:
```
deb https://sim.debiancn.org/skylake sid main contrib non-free
```

## Project Guideline (Draft)

#### Principle

Rebuilding all packages with `-march=native` is pointless and wasting
electricity. SIMDebian aims to a specific set of software that are
highly valuable to be compiled into vectorized code.

#### Workflow

The workflow is based on `git-buildpackage`. The three branchs from upstream (`master`, `upstream`, `pristine-tar`)
should not be modified. We make modifications on the `simd` branch, and append `+simd` to the debian revision number.
Alternatively, the `simd` mark can be replaced by specific ISA names e.g. `avx`, `avx2`, `neon`, etc. when that makes sense.

Candidate version schemes:

* `dpkg/now 1.19.0.5+simd.skylake`
* `dpkg/now 1.19.0.5+simd.haswell`
* `dpkg/now 1.19.0.5+simd.nehalem`

## [Benchmakrs](./benchmarks)

To proof this project is useful we need some benchmark data.

## Core Packages

#### Development

* Dpkg https://github.com/SIMDebian/dpkg/tree/simd

We forked dpkg to add SIMD buildflag support. With the patched dpkg, one just
need to rebuild any Debian package without change to enable new instruction
sets. Changes to code such as follows are not required.

```diff
-export DEB_CXXFLAGS_MAINT_APPEND =
+export DEB_CXXFLAGS_MAINT_APPEND = -march=native
```
