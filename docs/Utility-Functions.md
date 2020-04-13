* [Overview](#overview)
* [API](#api)
  * [yaksa_get_size](#yaksa_get_size)
  * [yaksa_get_true_extent](#yaksa_get_true_extent)
  * [yalsa_get_extent](#yaksa_get_extent)

# Overview
Editing utilities are used to extract information from datatypes. For examples, to get the size of the pack buffer for a non-contiguous datatype or the size of the buffer targeted by an unpack operation. This document describes the utility APIs in detail.

# API
## yaksa_get_size()
```c
int yaksa_get_size(yaksa_type_t  type, 
                   uintptr_t   * size)
```
* Get the size (in number of bytes) in the datatype 
* Parameters
  * [in] `type`: the datatype whose size is being requested
  * [out] `size`: the size of the datatype
* Returned values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

***

The following example shows how to get the pack buffer size for a non-contiguous datatype.

```c
#include <yaksa.h>

int main()
{
   int rc;
   int count; /* count of non_contig_type */
   int input_matrix[64];
   yaksa_type_t non_contig_type;
   yaksa_init();

   /* create datatype */

   uintptr_t size;
   rc = yaksa_get_size(non_contig_type, &size);
   assert(rc == YAKSA_SUCCESS);

   void *pack_buf = malloc(size * count);
   
   /* pack data from input_matrix into pack_buf */

   free(pack_buf);
   yaksa_free(non_contig_type);
   yaksa_finalize();
   return rc;
}
```
## yaksa_get_true_extent()
```c
int yaksa_get_true_extent(yaksa_type_t  type,
                          intptr_t    * lb, 
                          uintptr_t   * extent)
```
* Get the true extent (true span) of the datatype 
* Parameters
  * [in] `type`: the datatype whose extent is being requested
  * [out] `lb`: the lower bound of the datatype (only used to calculate the extent; does not change where the buffer points to) 
  * [out] `extent`: the extent of the datatype (represents the distance between the lowest and highest points of the datatype which can be larger than the size of the datatype, if the layout is non-contiguous)
* Returned values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

***

The true lower bound and true extent represent the boundaries of the datatype. The true lower bound is the lowest point in the datatype and the upper bound (`true_lb + true_extent`) is the highest. These points are immutable and, if the datatype has not been resized, can be used to figure out the size of the source buffer the datatype refers to (or the size of the buffer targeted by an unpack operation). An example is provided in the following.

```c
#include <yaksa.h>

int main()
{
   int rc;
   int count; /* count of non_contig_type */
   int input_matrix[64];
   yaksa_type_t non_contig_type;
   yaksa_init();

   /* create datatype */

   intptr_t lb;
   uintptr_t extent;
   rc = yaksa_get_true_extent(non_contig_type, &lb, &extent);
   assert(rc == YAKSA_SUCCESS);

   void *unpack_buf = malloc(extent * count);
   
   /* unpack data from input_matrix into unpack_buf */

   free(unpack_buf);
   yaksa_free(non_contig_type);
   yaksa_finalize();
   return rc;
}
```

## yaksa_get_extent()
```c
int yaksa_get_extent(yaksa_type_t  type, 
                     intptr_t    * lb,
                     uintptr_t   * extent)
```
* Get the extent (span) of the datatype
* Parameters
  * [in] `type`: the datatype whose extent is being requested
  * [out] `lb`: the lower bound of the datatype (only used to calculate the extent; does not change where the buffer points to)
  * [out] `extent`: the extent of the datatype (represents the distance between the lowest and highest points of the datatype. Can be larger than the true extent of the datatype for subarrays or if the lb and ub values were modified by creating a resized datatype)
* Returned values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

***

The lower bound and extent represent the fictitious boundaries of the datatype (unlike true boundaries previously discussed). When a new datatype is created from a non-resized type (a type not created using the resize API presented in [Creating New Datatypes](https://github.com/pmodels/yaksa/wiki/Creating-New-Datatypes)), these are set to their true counterparts. However, if the user has changed them using the Yaksa resize API, these will be different. Furthermore, if the resized type is used to create a new type, or is used in another Yaksa routine with a count greater than 1, the resulting type size will be affected as following shown.

```c
#include <yaksa.h>

int main()
{
   int rc;
   int count; /* count of non_contig_type */
   int input_matrix[64];
   yaksa_type_t non_contig_type;
   yaksa_init();

   /* create datatype */

   intptr_t lb, true_lb;
   uintptr_t extent, true_extent;
   rc = yaksa_get_extent(non_contig_type, &lb, &extent);
   assert(rc == YAKSA_SUCCESS);
   rc = yaksa_get_true_extent(non_contig_type, &true_lb, &true_extent);
   assert(rc == YAKSA_SUCCESS);

   int size = (extent * count) + true_extent - extent;
   void *unpack_buf = malloc(size);
   
   /* unpack data from input_matrix into unpack_buf */

   free(unpack_buf);
   yaksa_free(non_contig_type);
   yaksa_finalize();
   return rc;
}
```

The previous example reduces to the true boundaries example when type is not resized. Indeed, in this case `true_extent` and `extent` are the same and `size = true_extent * count`.