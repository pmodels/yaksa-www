* [API](#api)
  * [yaksa_ipack](#yaksa_ipack)
  * [yaksa_iunpack](#yaksa_iunpack)
  * [yaksa_request_wait](#yaksa_request_wait)
  * [yaksa_request_test](#yaksa_request_test)
* [Examples](#examples)
  * [Contig Layout](#contig-layout)
  * [Hvector Layout](#hvector-layout)
  * [Hindexed Block Layout](#hindexed-block-layout)
  * [Hindexed Layout](#hindexed-layout)
  * [Resized Layout](#resized-layout)
  * [Partial Pack and Unpack](#partial-pack-and-unpack)

# API
## yaksa_ipack()
```c
int yaksa_ipack(const void *inbuf, uintptr_t incount, yaksa_type_t type,
                uintptr_t inoffset, void *outbuf, uintptr_t max_pack_bytes,
                uintptr_t *actual_pack_bytes, yaksa_request_t *request)
```

* Pack the data represented by (incount, type) tuple into a contiguous buffer
* Parameters
  * [in] `inbuf`: input buffer from which data is being packed
  * [in] `incount`: number of elements of the datatype representing the layout
  * [in] `type`: datatype representing the layout
  * [in] `inoffset`: number of bytes to skip from the layout represented by the (incount, type) tuple
  * [out] `outbuf`: output buffer in which data is being packed
  * [in] `max_pack_bytes`: maximum number of bytes that can be packed in the output buffer
  * [out] `actual_pack_bytes`: actual number of bytes that were packed in the output buffer
  * [out] `request`: request handle associated with the operation (`YAKSA_REQUEST__NULL` if the request already completed)
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_iunpack()
```c
int yaksa_iunpack(const void *inbuf, uintptr_t insize, void *outbuf,
                  uintptr_t outcount, yaksa_type_t type, uintptr_t outoffset,
                  yaksa_request_t *request)
```

* Unpack data from a contiguous buffer into a buffer represented by the (incount, type) touple
* Parameters
  * [in] `inbuf`: input buffer from which data is being unpacked
  * [in] `insize`: number of bytes in the input buffer
  * [out] `outbuf`: output buffer into which data is being unpacked
  * [out] `outcount`: number of elements of the data representing the layout
  * [in] `type`: datatype representing the layout
  * [out] `outoffset`: number of bytes to skip from the layout represented by the (incount, type) tuple
  * [out] `request`: request handle associated with the operation (`YAKSA_REQUEST__NULL` if the request already completed)
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_request_wait()
```c
int yaksa_request_wait(yaksa_request_t request)
```

* Wait till a request has completed
* Parameters
  * [in] `request`: the request object that needs to be waited up on
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_request_test()
```c
int yaksa_request_test(yaksa_request_t request, int *completed)
```
* Test to see if a request has completed
* Parameters
  * [in] `request`: the request object that needs to be tested
  * [out] `completed`: flag to tell the caller whether the request object has completed
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

# Examples
The examples in this section build on the examples presented in [Datatype Creation](https://github.com/pmodels/yaksa/wiki/Datatype-Creation). They show how the produced data in the pack buffer looks like when considering different layouts. Moreover, they also show how to do partial pack and unpack. This feature is useful when the original data has to be sent to another process over the network, for example, and the communication library cannot transfer the all data at once. In this case smaller portions of the data have to be transferred one after the other in sequence.

## Contig Layout
Packing data using a contiguous layout has the effect of making an exact copy of the original data into the pack buffer, up to the number of bytes defined by the layout. Similarly, unpacking data using a contiguous layout has the effect of making an exact copy of the data in the pack buffer into the target buffer. The following code shows how to pack and unpack using the contiguous layout and the input data used in previous examples.

```c
#include <yaksa.h>

int main()
{
    int rc;
    int matrix[64]; /* initialized with data from previous example */
    int pack_buf[64];
    int unpack_buf[64];
    yaksa_type_t contig;

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    /* For layout creation see corresponding example */

    yaksa_request_t request;
    uintptr_t actual_pack_bytes;

    /* start packing */
    rc = yaksa_ipack(matrix, 1, contig, pack_buf, 256, &actual_pack_bytes, &request);
    assert(rc == YAKSA_SUCCESS);

    /* wait for packing to complete */
    rc = yaksa_request_wait(request);
    assert(rc == YAKSA_SUCCESS);

    /* start unpacking */
    rc = yaksa_iunpack(pack_buf, 256, unpack_buf, 256, contig, 0, &request);
    assert(rc == YAKSA_SUCCESS);

    /* wait for unpacking to complete */
    rc = yaksa_request_wait(request);
    assert(rc == YAKSA_SUCCESS);

    yaksa_free(contig);

    yaksa_finalize();
    return 0;
}
```

In the previous code the `yaksa_ipack/iunpack` functions start the packing/unpacking. These functions have non-blocking semantics and return a request object that can be used to check when the packing/unpacking has completed. The reason for having non-blocking semantics is that some target architectures, such as GPUs, support asynchronous memory copies. The previous code produces the following data in the pack/unpack buffers.

```c
pack_buf = unpack_buf =
 0  1  2  3  4  5  6  7
 8  9 10 11 12 13 14 15
16 17 18 19 20 21 22 23
24 25 26 27 28 29 30 31
32 33 34 35 36 37 38 39
40 41 42 43 44 45 46 47
48 49 50 51 52 53 54 55
56 57 58 59 60 61 62 63
```

## Hvector Layout
The following code shows how to pack and unpack using the hvector layout and the input data used in previous examples.

```c
#include <yaksa.h>

int main()
{
    int rc;
    int matrix[64]; /* initialized with data from previous example */
    int pack_buf[64];
    int unpack_buf[64];
    yaksa_type_t hvector;

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    /* For layout creation see corresponding example */

    yaksa_request_t request;
    uintptr_t actual_pack_bytes;

    /* start packing.
     * note that we can request more bytes in max_pack_bytes and will
     * get the correct number of packed bytes in actual_pack_bytes */
    rc = yaksa_ipack(matrix, 1, hvector, pack_buf, 256, &actual_pack_bytes, &request);
    assert(rc == YAKSA_SUCCESS);

    /* wait for packing to complete */
    rc = yaksa_request_wait(request);
    assert(rc == YAKSA_SUCCESS);

    /* start unpacking */
    rc = yaksa_iunpack(pack_buf, 32, unpack_buf, 1, hvector, 0, &request);
    assert(rc == YAKSA_SUCCESS);

    /* wait for unpacking to complete */
    rc = yaksa_request_wait(request);
    assert(rc == YAKSA_SUCCESS);

    yaksa_free(hvector);

    yaksa_finalize();
    return 0;
}
```

The previous code produces the following data in the pack/unpack buffers.

```c
pack_buf=
 0  8 16 24 32 40 48 56
 0  0  0  0  0  0  0  0
 0  0  0  0  0  0  0  0
 0  0  0  0  0  0  0  0
 0  0  0  0  0  0  0  0
 0  0  0  0  0  0  0  0
 0  0  0  0  0  0  0  0
 0  0  0  0  0  0  0  0

unpack_buf=
 0  0  0  0  0  0  0  0
 8  0  0  0  0  0  0  0
16  0  0  0  0  0  0  0
24  0  0  0  0  0  0  0
32  0  0  0  0  0  0  0
40  0  0  0  0  0  0  0
48  0  0  0  0  0  0  0
56  0  0  0  0  0  0  0
```

## Hindexed Block Layout
The following code shows how to pack and unpack using the hindexed block layout and the input data used in previous examples.

```c
#include <yaksa.h>

int main()
{
    int rc;
    int matrix[64]; /* initialized with data from previous example */
    int pack_buf[64];
    int unpack_buf[64];
    yaksa_type_t hindx_block;

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    /* For layout creation see corresponding example */

    yaksa_request_t request;
    uintptr_t actual_pack_bytes;

    /* start packing */
    rc = yaksa_ipack(matrix, 1, hindx_block, pack_buf, 256, &actual_pack_bytes, &request);
    assert(rc == YAKSA_SUCCESS);

    /* wait for packing to complete */
    rc = yaksa_request_wait(request);
    assert(rc == YAKSA_SUCCESS);

    /* start unpacking */
    rc = yaksa_iunpack(pack_buf, 128, unpack_buf, 1, hindx_block, 0, &request);
    assert(rc == YAKSA_SUCCESS);

    /* wait for unpacking to complete */
    rc = yaksa_request_wait(request);
    assert(rc == YAKSA_SUCCESS);

    yaksa_free(hindx_block);

    yaksa_finalize();
    return 0;
}
```

The previous code produces the following data in the pack/unpack buffers.

```c
pack_buf=
 4  5  6  7 12 13 14 15
20 21 22 23 28 29 30 31
32 33 34 35 40 41 42 43
48 49 50 51 56 57 58 59
 0  0  0  0  0  0  0  0
 0  0  0  0  0  0  0  0
 0  0  0  0  0  0  0  0
 0  0  0  0  0  0  0  0

unpack_buf=
 0  0  0  0  4  5  6  7
 0  0  0  0 12 13 14 15
 0  0  0  0 20 21 22 23
 0  0  0  0 28 29 30 31
32 33 34 35  0  0  0  0
40 41 42 43  0  0  0  0
48 49 50 51  0  0  0  0
56 57 58 59  0  0  0  0
```

## Hindexed
The following code shows how to pack and unpack using the hindexed layout and the input data used in previous examples.

```c
#include <yaksa.h>

int main()
{
    int rc;
    int matrix[64]; /* initialized with data from previous example */
    int pack_buf[64];
    int unpack_buf[64];
    yaksa_type_t hindexed;

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    /* For layout creation see corresponding example */

    yaksa_request_t request;
    uintptr_t actual_pack_bytes;

    /* start packing */
    rc = yaksa_ipack(matrix, 1, hindexed, pack_buf, 256, &actual_pack_bytes, &request);
    assert(rc == YAKSA_SUCCESS);

    /* wait for packing to complete */
    rc = yaksa_request_wait(request);
    assert(rc == YAKSA_SUCCESS);

    /* start unpacking */
    rc = yaksa_iunpack(pack_buf, 84, unpack_buf, 1, hindexed, 0, &request);
    assert(rc == YAKSA_SUCCESS);

    /* wait for unpacking to complete */
    rc = yaksa_request_wait(request);
    assert(rc == YAKSA_SUCCESS);

    yaksa_free(hindexed);

    yaksa_finalize();
    return 0;
}
```

The previous code produces the following data in the pack/unpack buffers.

```c
pack_buf=
 9 18 19 26 27 36 37 38
39 44 45 46 47 52 53 54
55 60 61 62 63  0  0  0
 0  0  0  0  0  0  0  0
 0  0  0  0  0  0  0  0
 0  0  0  0  0  0  0  0
 0  0  0  0  0  0  0  0
 0  0  0  0  0  0  0  0

unpack_buf=
 0  0  0  0  0  0  0  0
 0  9  0  0  0  0  0  0
 0  0 18 19  0  0  0  0
 0  0 26 27  0  0  0  0
 0  0  0  0 36 37 38 39
 0  0  0  0 44 45 46 47
 0  0  0  0 52 53 54 55
 0  0  0  0 60 61 62 63
```

## Resized Layout
The following code shows how to pack and unpack using the transposed layout and the input data used in previous examples.

```c
#include <yaksa.h>

int main()
{
    int rc;
    int matrix[64]; /* initialized with data from previous example */
    int pack_buf[64];
    int unpack_buf[64];
    yaksa_type_t vector;
    yaksa_type_t vector_resized;
    yaksa_type_t transpose;

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    /* For layout creation see corresponding example */

    yaksa_request_t request;
    uintptr_t actual_pack_bytes;

    /* start packing */
    rc = yaksa_ipack(matrix, 1, transpose, pack_buf, 256, &actual_pack_bytes, &request);
    assert(rc == YAKSA_SUCCESS);

    /* wait for packing to complete */
    rc = yaksa_request_wait(request);
    assert(rc == YAKSA_SUCCESS);

    /* start unpacking */
    rc = yaksa_iunpack(pack_buf, 256, unpack_buf, 1, transpose, 0, &request);
    assert(rc == YAKSA_SUCCESS);

    /* wait for unpacking to complete */
    rc = yaksa_request_wait(request);
    assert(rc == YAKSA_SUCCESS);

    yaksa_free(vector);
    yaksa_free(vector_resized);
    yaksa_free(transpose);

    yaksa_finalize();
    return 0;
}
```

The previous code produces the following data in the pack/unpack buffers.

```c
pack_buf=
 0  8 16 24 32 40 48 56
 1  9 17 25 33 41 49 57
 2 10 18 26 34 42 50 58
 3 11 19 27 35 43 51 59
 4 12 20 28 36 44 52 60
 5 13 21 29 37 45 53 61
 6 14 22 30 38 46 54 62
 7 15 23 31 39 47 55 63

unpack_buf=
 0  1  2  3  4  5  6  7
 8  9 10 11 12 13 14 15
16 17 18 19 20 21 22 23
24 25 26 27 28 29 30 31
32 33 34 35 36 37 38 39
40 41 42 43 44 45 46 47
48 49 50 51 52 53 54 55
56 57 58 59 60 61 62 63
```

The unpack buffer is identical to the original input buffer as a double transposition returns the original matrix.

## Partial Pack and Unpack
As already said, there are cases in which pack/unpack might need to be performed to/from a buffer that can only contain a part of the original data. This is the case, for example, when transferring a very large buffer over the network. In such case it is more efficient to break up the packing/unpacking into smaller chunks and overlap the packing/unpacking of the current chunk with the transfer of the previous/next. This overlap between memory copy and network transfer can be achieved precisely using the partial packing/unpacking feature offered by Yaksa as shown in the following example.

```c
#include <yaksa.h>

int main()
{
    int rc;
    int matrix[64]; /* initialized with data from previous example */
    int pack_buf[64];
    int unpack_buf[64];
    yaksa_type_t hindx_block;

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    /* For layout creation see corresponding example */

    yaksa_request_t request;
    uintptr_t actual_pack_bytes;

    uintptr_t chunk = 64;
    for (uintptr_t pos = 0; pos < 128; pos += chunk) {
        /* start partial packing */
        rc = yaksa_ipack(matrix, 1, hindx_block, pos, pack_buf,
                         chunk, &actual_pack_bytes, &request);
        assert(rc == YAKSA_SUCCESS);

        /* wait for packing to complete */
        rc = yaksa_request_wait(request);
        assert(rc == YAKSA_SUCCESS);

        /* start partial unpacking */
        rc = yaksa_iunpack(pack_buf, chunk, unpack_buf, 1, 
                           hindx_block, pos, &request);
        assert(rc == YAKSA_SUCCESS);

        /* wait for unpacking to complete */
        rc = yaksa_request_wait(request);
        assert(rc == YAKSA_SUCCESS);
    }

    yaksa_free(hindx_block);

    yaksa_finalize();
    return 0;
}
```

The previous code is similar to the hindexed block example. The difference is that only half of the data in the input buffer is packed/unpacked at every iteration of the for loop.