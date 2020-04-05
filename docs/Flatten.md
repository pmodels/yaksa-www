* [API](#api)
  * [yaksa_flatten_size](#yaksa_flatten_size)
  * [yaksa_flatten](#yaksa_flatten)
  * [yaksa_unflatten](#yaksa_unflatten)
* [Examples](#examples)

# API
## yaksa_flatten_size()
```c
int yaksa_flatten_size(yaksa_type_t type, uintptr_t *flattened_type_size)
```

* Number of bytes that a flattened representation of the datatype would take
* Parameters
  * [in] `type`: datatype to be flattened
  * [out] `flattened_type_size`: number of bytes the flattened type would take
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

## yaksa_flatten()
```c
int yaksa_flatten(yaksa_type_t type, void *flattened_type)
```

* Flatten the datatype into a form that can be sent to other processes in a multiprocessor environment
* Parameters
  * [in] `type`: datatype to be flattened
  * [out] `flattened_type`: the flattened representation of the datatype
* Return values
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

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