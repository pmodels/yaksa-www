* [API](#api)
  * [yaksa_iov_len](#yaksa_iov_len)
  * [yaksa_iov](#yaksa_iov)
* [Examples](#examples)

# API
## yaksa_iov_len()
```c
int yaksa_iov_len(uintptr_t count, yaksa_type_t type, uintptr_t *iov_len)
```

* Get the number of contiguous segments in the (count, type) tuple
* Parameters
  * [in] `count`: number of elements of the datatype representing the layout
  * [in] `type`: datatype representing the layout
  * [out] `iov_len`: number of contiguous segments in the (count, type) tuple
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_iov()
```c
int yaksa_iov(const char *buf, uintptr_t count, yaksa_type_t type,
              uintptr_t iov_offset, struct iovec *iov, uintptr_t max_iov_len,
              uintptr_t *actual_iov_len)
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
