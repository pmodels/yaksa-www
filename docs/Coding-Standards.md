# General Principles
Coding standards are intended to make this project more successful and enjoyable.  We now use our custom coding style.  This style is based on [K&R style](https://en.wikipedia.org/wiki/Indent_style#K.26R_style) with four spaces for an indent.

# Before Creating a PR

The Yaksa project uses a style checker. Before you create a PR, please pass the following script:
```
maint/code-cleanup.sh YOURCODE
```

If you want to perform this clean-up script through all your code files in the current directory, type as follows:
```
maint/code-cleanup.sh --all --recursive
```
You will find a test failure if your PR does not comply with this style checker.

# Basic Source File Structure
All source files follow similar structure.
```c
#include "yaksi.h"

int yaksi_template(yaksa_type_t type, size_t *size)
{
    int rc = ABT_SUCCESS;

    if (type == YAKSA_TYPE__NULL) {
        rc = YAKSA_ERR__OTHER;
        goto fn_fail;
    }

    /* Implementation */
    /* ... */

fn_exit:
    return rc;

fn_fail:
    goto fn_exit;
}
```

## Copyright
`COPYRIGHT` file exists in top-level directory.  If the default copyright is applied to the source file, the following can be used:
```c
/*
 * Copyright (C) by Argonne National Laboratory
 *     See COPYRIGHT in top-level directory
 */
```

## Header files
Header files should have their contents wrapped within an `ifndef` of the form
```c
#ifndef FILENAME_H_INCLUDED
#define FILENAME_H_INCLUDED
...
#endif /* FILENAME_H_INCLUDED */
```
where `FILENAME` is the name of the file, in upper case.

## Column width
Lines within files should be limited to 80 columns.

## Comments
Use `/* */` and avoid `//` for writing comments.

If you want to write comments for doxygen documentation, refer to [Code Documentation](https://github.com/pmodels/yaksa/wiki/Code-Documentation).

## Code cleanup script
The script `maint/code-cleanup.sh` can be used to cleanup whitespace, comments, and line-wrapping in existing source files. It is a good idea to run it on any newly created files in the git repo.

# Naming Convention
Naming is important to distinguish internal implementation from public interface.  Yaksa uses prefix rules to differentiate codes according to their intention, i.e., public or internal.
All characters in names should be lower-case. For example,
```c
int yaksa_create_vector()
```

## Type and routine names
Names for types and routines should be chosen in a way that (1) is descriptive of their function and (2) is clearly distinct from any name that may be used by another runtime library or user code.  To achieve this, we have chosen to reserve a few prefixes for the use of the Argobots implementation:

| Prefix     | Description |
| ---------- | ----------- |
| `yaksa_`   | This is used for the public interface exposed to users.  All public data types and APIs should start with this prefix. |
| `yaksi_`   | This is used for internal data types and routines used by the yaksa frontend to implement the Argobots public APIs.  This does not include backend-specific types and routines. |
| `yaksur_`  | This is used for backend-specific data types and routines needed to implement `yaksa_` and `yaksi_` routines. It plays a role of an interface to hide internal device-specific implementation. |
| `yaksuri_` | This is used for internal implementation for backend-specific types and routines. It implements `yaksur_` types and routines according to the target architecture. |
| `yaksu_`  | This is used for utility routines, which support general functions not only limited in implementation of yaksa.  Therefore, utility routines should be able to be compiled independently. |

## Macro and enum names
Just like type and routine names, it is important to pick macro and enum names to avoid possible conflicts with others defined in system header files.

| Prefix/Suffix | Description |
| ------------- | ----------- |
| `YAKSA_`      | Used for public interface. |
| `YAKSI_`, `YAKSUR_`, `YAKSURI_`, `YAKSU_` | Used for internal implementation. Each prefix has same meaning as one in type and routine names.  |
| `_H_INCLUDED` | This suffix should be used as the test for including a header (`.h`) file. |

Every macro and enum name starting with above prefixes should be all uppercase.

## Variables
Some prefixes are used to distinguish global variables and pointers. For regular variables, currently there is no strict rule for naming them.


# Error Handling
TBD