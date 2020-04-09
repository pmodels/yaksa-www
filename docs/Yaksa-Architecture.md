![](https://pmodels.github.io/yaksa-www/images/yaksa-arch.svg)

Yaksa uses a three-layer architecture composed of a frontend layer, a
backend glue layer, and a backend device layer.

The frontend layer is independent of the memory subsystem that is
used.  It implements functionality that does not require knowledge of
the actual memory subsystem, such as datatype to IOV conversion and
flattening/unflattening of datatypes.  It also handles quirky inputs
to functionality that require knowledge about the actual memory
subsystem, such as pack/unpack, by converting irregular inputs into a
collection of smaller regular inputs for the backend to handle.

The backend glue layer handles interactions that require multiple
backend devices.  For example, packing noncontiguous data from a GPU
on to a contiguous buffer on the CPU.

The backend device layer handles the actual pack/unpack computations.
This layer is specific to each memory subsystem.