* [Overview](#overview)
* [API](#api)
  * [yaksa_create_contig](#yaksa_create_contig)
  * [yaksa_create_dup](#yaksa_create_dup)
  * [yaksa_create_resized](#yaksa_create_resized)
  * [yaksa_create_vector](#yaksa_create_vector)
  * [yaksa_create_hvector](#yaksa_create_hvector)
  * [yaksa_create_indexed_block](#yaksa_create_indexed_block)
  * [yaksa_create_hindexed_block](#yaksa_create_hindexed_block)
  * [yaksa_create_indexed](#yaksa_create_indexed)
  * [yaksa_create_hindexed](#yaksa_create_hindexed)
  * [yaksa_create_struct](#yaksa_create_struct)
  * [yaksa_create_subarray](#yaksa_create_subarray)
  * [yaksa_free](#yaksa_free)
* [Examples](#examples)
  * [Contig Layout](#contig-layout)
  * [Hvector Layout](#hvector-layout)
  * [Hindexed Block Layout](#hindexed-block-layout)
  * [Hindexed Layout](#hindexed-layout)
  * [Struct Layout](#struct-layout)
  * [Resized Layout](#resized-layout)

# Overview
This page describes the APIs provided by Yaksa to create datatypes. Similarly to MPI datatypes, Yaksa defines native (or builtin) datatypes as well as derived datatypes. Native datatypes are basic building blocks that Yaksa can use to construct arbitrarily complex data layout representations using Yaksa derived datatypes.

# API
## yaksa_create_contig()
```c
int yaksa_create_contig(int count, yaksa_type_t oldtype, yaksa_type_t *newtype)
```

* Creates a contig layout.
* Parameters
  * [in] `count`: number of elements of the old type
  * [in] `oldtype`: base datatype forming each element in the contig
  * [out] `newtype`: final generated type
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_create_dup()
```c
int yaksa_create_dup(yaksa_type_t oldtype, yaksa_type_t *newtype)
```

* Create a copy of the old datatype
* Parameters
  * [in] `oldtype`: base datatype being dup'ed
  * [out] `newtype`: final generated type
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_create_resized()
```c
int yaksa_create_resized(yaksa_type_t oldtype, intptr_t lb, uintptr_t extent,
                         yaksa_type_t *newtype)
```

* Create a resized layout with updated lower bound and extent
* Parameters
  * [in] `oldtype`: base datatype forming each element in new type
  * [in] `lb`: new lower bound to use
  * [in] `extent`: new extent to use
  * [out] `newtype`: final generated type
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_create_vector()
```c
int yaksa_create_vector(int count, int blocklength, int stride, yaksa_type_t oldtype,
                        yaksa_type_t *newtype)
```

* Create a vector layout
* Parameters
  * [in] `count`: number of blocks in the vector
  * [in] `blocklength`: length of each block
  * [in] `stride`: increment from the start of one block to another (represented in terms of the count of `oldtype`)
  * [in] `oldtype`: base datatype forming each element in new type
  * [out] `newtype`: final generated type
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_create_hvector()
```c
int yaksa_create_hvector(int count, int blocklength, intptr_t stride, 
                         yaksa_type_t oldtype, yaksa_type_t *newtype)
```
* Create a hvector layout
* Parameters
  * [in] `count`: number of blocks in the vector
  * [in] `blocklength`: length of each block
  * [in] `stride`: increment from the start of one block to another (represented in bytes)
  * [in] `oldtype`: base datatype forming each element in new type
  * [out] `newtype`: final generated type
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_create_indexed_block()
```c
int yaksa_create_indexed_block(int count, int blocklength, 
                               const int *array_of_displacements,
                               yaksa_type_t oldtype, yaksa_type_t *newtype)
```

* Create a block-indexed layout
* Parameters
  * [in] `count`: number of blocks in the new type
  * [in] `blocklength`: length of each block
  * [in] `array_of_displacements`: starting offset to each block (represented in terms of the count of `oldtype`)
  * [in] `oldtype`: base datatype forming each element in the new type
  * [out] `newtype`: final generated type
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_create_hindexed_block()
```c
int yaksa_create_hindexed_block(int count, int blocklength, 
                                const intptr_t *array_of_displacements, 
                                yaksa_type_t oldtype, yaksa_type_t *newtype)
```
* Create a block-hindexed layout
* Parameters
  * [in] `count`: number of blocks in the new type
  * [in] `blocklength`: length of each block
  * [in] `array_of_displacements`: starting offset to each block (represented in bytes)
  * [in] `oldtype`: base datatype forming each element in the new type
  * [out] `newtype`: final generated type
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_create_indexed()
```c
int yaksa_create_indexed(int count, const int array_of_blocklengths, 
                         const int *array_of_displacements, yaksa_type_t oldtype, 
                         yaksa_type_t *newtype)
```

* Create an indexed layout
* Parameters
  * [in] `count`: number of blocks int he new type
  * [in] `array_of_blocklengths`: array of lengths of each block
  * [in] `array_of_displacements`: starting offset to each block (represented in terms of the count of `oldtype`)
  * [in] `oldtype`: base datatype forming each element in the new type
  * [out] `newtype`: final generated type
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_create_hindexed()
```c
int yaksa_create_hindexed(int count, const int array_of_blocklengths, 
                          const intptr_t *array_of_displacements, 
                          yaksa_type_t oldtype, yaksa_type_t *newtype)
```

* Create an hindexed layout
* Parameters
  * [in] `count`: number of blocks int he new type
  * [in] `array_of_blocklengths`: array of lengths of each block
  * [in] `array_of_displacements`: starting offset to each block (represented in bytes)
  * [in] `oldtype`: base datatype forming each element in the new type
  * [out] `newtype`: final generated type
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_create_struct()
```c
int yaksa_create_struct(int count, const int *array_of_blocklengths, 
                        const intptr_t *array_of_displacements, 
                        const yaksa_type_t *array_of_types, 
                        yaksa_type_t *newtype)
```

* Create a struct layout
* Parameters
  * [in] `count`: number of blocks in the new type
  * [in] `array_of_blocklengths`: array of lengths of each block
  * [in] `array_of_displacements`: starting offset to each block
  * [in] `array_of_types`: array of base datatypes forming each element in the new type
  * [out] `newtype`: final generated type
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_create_subarray()
```c
int yaksa_create_subarray(int ndims, const int *array_of_sizes, 
                          const int *array_of_subsizes, 
                          const int *array_of_starts,
                          yaksa_subarray_order_e order, 
                          yaksa_type_t oldtype, yaksa_type_t *newtype)
```

* Create a subarray layout
* Parameters
  * [in] `ndims`: number of dimensions in the array
  * [in] `array_of_sizes`: dimension sizes of the entire array
  * [in] `array_of_subsizes`: dimension sizes of the subarray
  * [in] `array_of_starts`: start location ("corner representing the start") of the subarray
  * [in] `order`: data layout order (C or Fortran)
  * [in] `oldtype`: base datatype forming each element of the new type
  * [out] `newtype`: final generated type
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_free()
```c
int yaksa_free(yaksa_type_t type)
```

* Free datatype
* Parameters
  * [in] `type`: the datatype being freed
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

# Examples
This section provides some practical example on how to create new data layouts using the Yaksa APIs described in the previous section. Simple examples are provided for each API. At the end this section also provides some real (more articulated) use case in which multiple APIs are being used to create the desired layouts. All the examples in this section (exception made for the struct datatype) produce layouts that are applied to the following input matrix:

```c
input_matrix =
 0  1  2  3  4  5  6  7
 8  9 10 11 12 13 14 15
16 17 18 19 20 21 22 23
24 25 26 27 28 29 30 31
32 33 34 35 36 37 38 39
40 41 42 43 44 45 46 47
48 49 50 51 52 53 54 55
56 57 58 59 60 61 62 63
```

Each element of the matrix is marked with the position of the element in memory.

## Contig Layout
The following example showcases the use `yaksa_create_contig` API to create a new contig layout.

```c
#include <yaksa.h>

int main()
{
    int rc;
    yaksa_type_t contig;

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    rc = yaksa_create_contig(64, YAKSA_TYPE__INT, &contig);
    assert(rc == YAKSA_SUCCESS);

    yaksa_free(contig);

    yaksa_finalize();
    return 0;
}
```
The code snippet creates a new contig layout containing 64 signed integer elements, thus the `contig` layout covers all the elements in the `input_matrix`. 

## Hvector Layout
The following example showcases the use `yaksa_create_hvector` API to create a new hvector layout.

```c
#include <yaksa.h>

int main()
{
    int rc;
    yaksa_type_t hvector;

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    rc = yaksa_create_hvector(8, 1, sizeof(int) * 8, YAKSA_TYPE__INT, &hvector);
    assert(rc == YAKSA_SUCCESS);

    yaksa_free(hvector);

    yaksa_finalize();
    return 0;
}
```

The created `hvector` layout contains 8 blocks, each made of 1 signed integer and with a distance between the first elements of two consecutive blocks of 8 signed integers (or 32 bytes). The same layout can also be obtained using `yaksa_create_vector` by replacing the number of bytes in the stride with the number of signed integer elements (i.e. 8). The `hvector` layout covers all the elements of the first column in `input_matrix` as following shown:

```c
input_matrix =
 0  x  x  x  x  x  x  x
 8  x  x  x  x  x  x  x
16  x  x  x  x  x  x  x
24  x  x  x  x  x  x  x
32  x  x  x  x  x  x  x
40  x  x  x  x  x  x  x
48  x  x  x  x  x  x  x
56  x  x  x  x  x  x  x
```

## Hindexed Block Layout
The following example showcases the use `yaksa_create_hindexed_block` API to create a new hindexed block layout.

```c
#include <yaksa.h>

int main()
{
    int rc;
    yaksa_type_t hindx_block;
    intptr_t array_of_displacements[8] = {
        sizeof(int) * 4 , sizeof(int) * 12, sizeof(int) * 20, sizeof(int) * 28,
        sizeof(int) * 32, sizeof(int) * 40, sizeof(int) * 48, sizeof(int) * 56};

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    rc = yaksa_create_hindexed_block(8, 4, array_of_displacements, YAKSA_TYPE__INT,
                                     &hindx_block);
    assert(rc == YAKSA_SUCCESS);

    yaksa_free(hindx_block);

    yaksa_finalize();
    return 0;
}
```

The `hindx_block` layout covers the following elements in `input_matrix`:

```c
input_matrix =
 x  x  x  x  4  5  6  7
 x  x  x  x 12 13 14 15
 x  x  x  x 20 21 22 23
 x  x  x  x 28 29 30 31
32 33 34 35  x  x  x  x
40 41 42 43  x  x  x  x
48 49 50 51  x  x  x  x
56 57 58 59  x  x  x  x
```

## Hindexed Layout
The following example showcases the use `yaksa_create_hindexed` API to create a new hindexed layout.

```c
#include <yaksa.h>

int main()
{
    int rc;
    yaksa_type_t hindexed;
    int array_of_blocklengths[7] = {
        1, 2, 2, 4, 4, 4, 4};
    intptr_t array_of_displacements[7] = {
        sizeof(int) * 9 ,
        sizeof(int) * 18, sizeof(int) * 26,
        sizeof(int) * 36, sizeof(int) * 44, sizeof(int) * 52, sizeof(int) * 60};

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    rc = yaksa_create_hindexed(7, array_of_blocklengths, array_of_displacements, 
                               YAKSA_TYPE__INT, &hindexed);
    assert(rc == YAKSA_SUCCESS);

    yaksa_free(hindexed);

    yaksa_finalize();
    return 0;
}
```

The `hindexed` layout covers the following elements in input matrix:

```c
input_matrix =
 x  x  x  x  x  x  x  x
 x  9  x  x  x  x  x  x
 x  x 18 19  x  x  x  x
 x  x 26 27  x  x  x  x
 x  x  x  x 36 37 38 39
 x  x  x  x 44 45 46 47
 x  x  x  x 52 53 54 55
 x  x  x  x 60 61 62 63
```

## Struct Layout
The following example showcases the use `yaksa_create_struct` API to create a new struct layout.

```c
#include <yaksa.h>

int main()
{
    int rc;
    yaksa_type_t str;
    yaksa_type_t array_of_types[4] = {
        YAKSA_TYPE__CHAR, 
        YAKSA_TYPE__INT, 
        YAKSA_TYPE__LONG, 
        YAKSA_TYPE__LONG_DOUBLE};
    int array_of_blocklengths[4] = {
        6, 3, 2, 1};
    intptr_t array_of_displacements[4] = {
        0,
        (sizeof(char) * 6),
        (sizeof(char) * 6) + (sizeof(int) * 3),
        (sizeof(char) * 6) + (sizeof(int) * 3) + (sizeof(long) * 2)};

    yaksa_init(); /* before any yaksa API is called the library
                     must be initialized */

    rc = yaksa_create_struct(4, array_of_blocklengths, array_of_displacements, 
                             array_of_types, &str);
    assert(rc == YAKSA_SUCCESS);

    yaksa_free(str);

    yaksa_finalize();
    return 0;
}
```

The created struct layout describes to the following C structure

```c
struct {
    char        a[6];
    int         b[3];
    long        c[2];
    long double d;
}
```

## Resized Layout
This example shows how to use a resized vector datatype to perform a matrix transposition. A matrix transposition layout can be created using the following code. 

```c
#include <yaksa.h>

int main()
{
    int rc;
    yaksa_type_t vector;
    yaksa_type_t vector_resized;
    yaksa_type_t transpose;

    yaksa_init();

    /* create a vector datatype for the elements in the matrix. The 
     * layout has 8 blocks (as the # of elements in a column), 1
     * element per block and a stride between blocks of 8 elements
     * (the # of elements in a row) */   
    rc = yaksa_create_vector(8, 1, 8, YAKSA_TYPE__INT, &vector);
    assert(rc == YAKSA_SUCCESS);

    /* change the extent of the datatype from 228 to 4 bytes so that the 
     * first element of a column starts after the first element in the 
     * previous instead of the last */
    rc = yaksa_create_resized(vector, 0, sizeof(int), &vector_resized);
    assert(rc == YAKSA_SUCCESS);

    /* create a transpose layout by lining up a number of resized
     * column layouts equal to the number of columns (i.e. 8) */
    rc = yaksa_create_contig(8, vector_resized, &transpose);
    assert(rc == YAKSA_SUCCESS);

    yaksa_free(vector);
    yaksa_free(vector_resized);
    yaksa_free(transpose);

    yaksa_finalize();
    return 0;
}
```

The previous code creates a vector type representing columns in the original matrix. After, it resizes the `extent` of the vector type using `yaksa_create_resized`. The `extent` marks the end of a datatype element and the start of the next, thus resizing the `extent` from the original 228 bytes to 4 can make columns interleave with each other in the final `transpose` layout, as following shown.

```c
/* with resizing */
input     = [0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 ...]
transpose = [0               8                     16 ...  vector_resized[0]
               1               9 ...                       vector_resized[1]
                 2               10 ...                    vector_resized[2]
                   3                11 ...                 vector_resized[3]
                     4                 12 ...              vector_resized[4]
                       5                  13 ...           vector_resized[5]
                         6                   14 ...        vector_resized[6]
                           7                    15 ...     vector_resized[7]     
```

Note that without the resizing of the `extent` the second row of the matrix would start with the element `57` instead of `1` and memory outside the matrix would be erroneously referenced after it.

[Pack and Unpack](https://github.com/pmodels/yaksa/wiki/Pack-and-Unpack) shows how the layouts presented in the above examples can be used with pack and unpack routines and the produced results.