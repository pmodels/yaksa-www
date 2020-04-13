* [Overview](#overview)
* [API](#api)
  * [yaksa_iov_len](#yaksa_iov_len)
  * [yaksa_iov](#yaksa_iov)
* [Examples](#examples)
  * [Iov Create](#Iov-create)
  * [Partitioned Iov Create](#partitioned-iov-create)

# Overview
The I/O vector representation is useful when packing becomes expensive. This happens when the amount of data to be packed is high and the memory copy cost becomes substantial compared with the cost of directly accessing single blocks of data in the buffer. Thus, if the data layout is characterized by large blocks of contiguous data, I/O vectors become a more viable representation. On the contrary, if blocks are small and there are many of them packing might be a more suitable strategy. Similarly to pack and unpack, I/O vector APIs also allow for partial datatype processing, as showcased by following examples. 

# API
## yaksa_iov_len()
```c
int yaksa_iov_len(uintptr_t      count,
                  yaksa_type_t   type,
                  uintptr_t    * iov_len)
```

* Get the number of contiguous segments in the (count, type) tuple
* Parameters
  * [in] `count`: number of elements of the datatype representing the layout
  * [in] `type`: datatype representing the layout
  * [out] `iov_len`: number of contiguous segments in the (count, type) tuple
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

***

## yaksa_iov()
```c
int yaksa_iov(const char   * buf,
              uintptr_t      count,
              yaksa_type_t   type,
              uintptr_t      iov_offset,
              struct iovec * iov,
              uintptr_t      max_iov_len,
              uintptr_t    * actual_iov_len)
```

* Convert the (count, type) tuple into an I/O vector (array of base pointer/length structure)
* Parameters
  * [in] `buf`: input buffer being used to create the iov
  * [in] `count`: number of elements of the datatype representing the layout
  * [in] `type`: datatype representing the layout
  * [in] `iov_offset`: number of contiguous segments to skip
  * [out] `iov`: the I/O vector that is being filled out
  * [in] `max_iov_len`: maximum number of iov elements that can be added to the vector
  * [out] `actual_iov_len`: actual number of iov elements that were added to the vector
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

# Examples
## Iov Create
The following example shows how to get the I/O vector representation for the Hindexed Block datatype defined on the input matrix used in previous examples (refer to [Datatype Creation](https://github.com/pmodels/yaksa/wiki/Datatype-Creation))

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
    yaksa_type_t hindx_block;
    intptr_t array_of_displacements[8] = {
        sizeof(int) * 4 , sizeof(int) * 12, sizeof(int) * 20, sizeof(int) * 28,
        sizeof(int) * 32, sizeof(int) * 40, sizeof(int) * 48, sizeof(int) * 56};

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    /* For layout creation see corresponding example */

    uintptr_t iov_num;
    rc = yaksa_iov_len(1, hindx_block, &iov_num);
    assert(rc == YAKSA_SUCCESS);

    struct iovec *iov_elem = malloc(sizeof(struct iovec) * iov_num);

    uintptr_t actual_iov_len;
    rc = yaksa_iov((const char *) matrix, 1, hindx_block, 0, iov_elem, iov_num,
                   &actual_iov_len);
    assert(rc == YAKSA_SUCCESS);

    free(iov_elem);
    yaksa_free(hindx_block);

    yaksa_finalize();
    return 0;
}
```

The code above will produce the following I/O vector

```c
num_iov = 8
iov_elem[0] => iov_len = 4; iov_base = [4 5 6 7]
iov_elem[1] => iov_len = 4; iov_base = [12 13 14 15]
iov_elem[2] => iov_len = 4; iov_base = [20 21 22 23]
iov_elem[3] => iov_len = 4; iov_base = [28 29 30 31]
iov_elem[4] => iov_len = 4; iov_base = [32 33 34 35]
iov_elem[5] => iov_len = 4; iov_base = [40 41 42 43]
iov_elem[6] => iov_len = 4; iov_base = [48 49 50 51]
iov_elem[7] => iov_len = 4; iov_base = [56 57 58 59]
```

## Partitioned Iov Create
I/O vectors can also be used with file I/O scatter/gather APIs, such as POSIX [readv](http://man7.org/linux/man-pages/man2/readv.2.html "POSIX readv API") and [writev](http://man7.org/linux/man-pages/man2/writev.2.html "POSIX writev API"). Latest POSIX versions pose a limit of 1024 on the number of iov that can be passed to such APIs. For this reason `yaksa_iov` also allows for the possibility of creating iov representations for parts of the input layout. One such example, using the previous layout, is following shown.

```c
#include <yaksa.h>

int main()
{
    int rc;
    int matrix[64] = { /* as for previous example */ };
    yaksa_type_t hindx_block;
    intptr_t array_of_displacements[8] = {
        sizeof(int) * 4 , sizeof(int) * 12, sizeof(int) * 20, sizeof(int) * 28,
        sizeof(int) * 32, sizeof(int) * 40, sizeof(int) * 48, sizeof(int) * 56};

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    /* For layout creation see corresponding example */

    uintptr_t iov_num;
    rc = yaksa_iov_len(1, hindx_block, &iov_num);
    assert(rc == YAKSA_SUCCESS);

    /* only process half of the iov at a time */
    iov_num /= 2;

    struct iovec *iov_elem = malloc(sizeof(struct iovec) * iov_num);

    uintptr_t actual_iov_len;
    for (uintptr_t i = 0; i < 2; i++) {
        rc = yaksa_iov((const char *) matrix, 1, hindx_block, i * num_iov, iov_elem, iov_num,
                       &actual_iov_len);
        assert(rc == YAKSA_SUCCESS);
    }

    free(iov_elem);
    yaksa_free(hindx_block);

    yaksa_finalize();
    return 0;
}
```

The code above will produce the following I/O vector

```c
num_iov = 4

/* iteration i = 0 */
iov_elem[0] => iov_len = 4; iov_base = [4 5 6 7]
iov_elem[1] => iov_len = 4; iov_base = [12 13 14 15]
iov_elem[2] => iov_len = 4; iov_base = [20 21 22 23]
iov_elem[3] => iov_len = 4; iov_base = [28 29 30 31]

/* iteration i = 1 */
iov_elem[0] => iov_len = 4; iov_base = [32 33 34 35]
iov_elem[1] => iov_len = 4; iov_base = [40 41 42 43]
iov_elem[2] => iov_len = 4; iov_base = [48 49 50 51]
iov_elem[3] => iov_len = 4; iov_base = [56 57 58 59]
```