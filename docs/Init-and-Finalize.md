* [Overview](#overview)
* [API](#api)
  * [yaksa_init](#yaksa_init)
  * [yaksa_finalize](#yaksa_finalze)

# Overview
The Yaksa library is thread safe. Thus, in a multi-threaded program, threads can call Yaksa routines (e.g. `yaksa_create_vector` or `yaksa_ipack/iunpack`) concurrently. The only exceptions are `yaksa_init` and `yaksa_finalize` that have to be called only by one thread. This document describes these routines.
 
# API
## yaksa_init()
```c
int yaksa_init(void)
```
* Returned value
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.

***

## yaksa_finalize()
```c
int yaksa_finalize(void)
```
* Returned value
  * On success, `YAKSA_SUCCESS` is returned.
  * On error, a non-zero error code is returned.