## Yaksa

Yaksa is a high-performance datatype engine for expressing, managing
and manipulating data present in noncontiguous memory regions.  It
provides portable abstractions for structured noncontiguous data
layouts that are much more comprehensive compared with traditional I/O
vectors.

Yaksa imitates parts of the MPI Datatype system, but adds additional
functionality that would allow it to be used independent of MPI. It
provides routines for packing/unpacking, creating I/O vectors (array
of contiguous segments) and flattening/unflattening datatypes into
process-portable formats.

Yaksa's backend includes support for CPUs as well as different GPUs.

Yaksa is open-source and is distributed under the [BSD-3
license](https://raw.githubusercontent.com/pmodels/yaksa/master/COPYRIGHT).
It is a community-developed software that anyone can contribute to
based on its [Contributor License
Agreement](https://github.com/pmodels/yaksa/wiki/Yaksa-Contributor-License-Agreement).
