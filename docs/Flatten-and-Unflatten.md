* [Overview](#overview)
* [API](#api)
  * [yaksa_flatten_size](#yaksa_flatten_size)
  * [yaksa_flatten](#yaksa_flatten)
  * [yaksa_unflatten](#yaksa_unflatten)
* [Examples](#examples)

# Overview
In some cases the datatype layout might need to be transferred to another process that performs the unpacking of the receive buffer into the final destination buffer. This is the case, for example, when using MPI one-sided communication such as `MPI_Put` or `MPI_Accumulate`. One possibility is to convert the origin datatype into an iov representation and transfer this to the target of the RMA operation. However, as already said when discussing the I/O vector APIs, the I/O vector representation might not always be the best from a performance standpoint. In these cases it is best to transfer the datatype directly. This can be achieved by flattening the datatype representation (tree) into a linear array of bytes inside a buffer.

# API
## yaksa_flatten_size()
```c
int yaksa_flatten_size(yaksa_type_t   type,
                       uintptr_t    * flattened_type_size)
```

* Number of bytes that a flattened representation of the datatype would take
* Parameters
  * [in] `type`: datatype to be flattened
  * [out] `flattened_type_size`: number of bytes the flattened type would take
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

***

## yaksa_flatten()
```c
int yaksa_flatten(yaksa_type_t   type,
                  void         * flattened_type)
```

* Flatten the datatype into a form that can be sent to other processes in a multiprocessor environment
* Parameters
  * [in] `type`: datatype to be flattened
  * [out] `flattened_type`: the flattened representation of the datatype
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

***

## yaksa_unflatten()
```c
int yaksa_unflatten(yaksa_type_t *type, const void *flattened_type)
```

* Unflatten the datatype into a full datatype
* Parameters
  * [out] `type`: datatype generated from the flattened type
  * [in] `flattened_type`: the flattened representation of the datatype
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

# Examples
The following code shows how this can be achieved using Yaksa flatten/unflatten APIs.

```c
#include <yaksa.h>

int main()
{
    int rc;
    int matrix[64] = {
        0,  1,  2,  3,  4,  5,  6,  7,
        8,  9, 10, 11, 12, 13, 14, 15,
       16, 17, 18, 19, 20, 21, 22, 23,
       24, 25, 26, 27, 28, 29, 30, 31,
       32, 33, 34, 35, 36, 37, 38, 39,
       40, 41, 42, 43, 44, 45, 46, 47,
       48, 49, 50, 51, 52, 53, 54, 55,
       56, 57, 58, 59, 60, 61, 62, 63};
    int pack_buf[64];
    int unpack_buf[64];
    yaksa_type_t hindx_block;
    intptr_t array_of_displacements[8] = {
        sizeof(int) * 4 , sizeof(int) * 12, sizeof(int) * 20, sizeof(int) * 28,
        sizeof(int) * 32, sizeof(int) * 40, sizeof(int) * 48, sizeof(int) * 56};

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    /* For layout creation see corresponding example */

    yaksa_request_t request;
    uintptr_t actual_pack_bytes;
    rc = yaksa_ipack(matrix, 1, hindx_block, 0, pack_buf, 128, &actual_pack_bytes, &request);
    assert(rc == YAKSA_SUCCESS);
    rc = yaksa_request_wait(request);
    assert(rc == YAKSA_SUCCESS);

    /* get buffer size for flatten type */
    uintptr_t flatten_size;
    yaksa_flatten_size(hindx_block, &flatten_size);

    void *flatten_type = malloc(flatten_size);
    yaksa_flatten(hindx_block, flatten_type);

    yaksa_type_t unflatten_type;
    yaksa_unflatten(&unflatten_type, flatten_type);

    /* get unflatten_type size */
    uintptr_t size;
    yaksa_get_size(unflatten_type, &size);

    rc = yaksa_iunpack(pack_buf, size, unpack_buf, 1, unflatten_type, 0, &request);
    assert(rc == YAKSA_SUCCESS);
    rc = yaksa_request_wait(request);
    assert(rc == YAKSA_SUCCESS);

    yaksa_free(hindx_block);
    yaksa_free(unflatten_type);
    free(flatten_type);

    yaksa_finalize();
    return 0;
}
```