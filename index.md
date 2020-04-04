## Yaksa

Yaksa is a high-performance datatype engine for expressing and
manipulating data present in noncontiguous memory regions. Yaksa
imitates parts of the MPI Datatype system, but adds additional
functionality that would allow it to be used independent of MPI. It
provides routines for packing/unpacking, creating I/O vectors (array
of contiguous segments) and flattening/unflattening datatypes into
process portable formats.

Yaksa's API includes supports for all of MPI datatypes, including
vectors, indexed, block indexed, structure, subarray, resized,
contiguous and dup'ed datatypes. It has built-in support for both
basic and pair-type datatypes.

Yaksa's backend includes support for CPUs as well as different GPUs.
