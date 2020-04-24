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

Yaksa is open-source and is distributed under the [BSD-3 license]({{
site.github.license_url }}).  It is a community-developed software
that anyone can contribute to based on its [Contributor License
Agreement]({{ site.github.cla_url }}).

## Supported Platforms

Yaksa has been tested on the following CPU architectures:

* x86_64
* ppc64

For GPU architectures, the following are supported:

* NVIDIA GPU via CUDA

Yaksa supports all C99 and POSIX 2001 compliant systems.  Specific
compilers that are supported are:

* GNU (>= 4.1)
* LLVM clang (>= 3.0)
* Intel (>= 10.1)
* PGI (>= 9.0)
* XL (>= 10.0)
* Sun Studio (>= 12)
* Fujitsu Softune

***

<a href="{{ site.github.repository_url }}">{{
site.github.repository_name }}</a> is maintained by the <a href="{{
site.github.owner_url }}">{{ site.github.owner_name }} group</a> at
the Argonne National Laboratory.

The contents of this website are &copy; 2020 under the terms of the <a
href="{{ site.github.license_url }}">yaksa license</a>.
