To compile Yaksa for debugging, use the following configuration options:
```
$ ./configure --enable-debug --enable-fast=O0 --disable-shared
```
Here, `--enable-debug --enable-fast=O0` will set the correct compile flags, and 'disable-shared' will tell the compiler to compile the program in static form.  Then you can use gdb directly for debugging the binary.
The following is a complete example of how to use gdb to debug scheduler_basic.
```
$ ./configure --enable-debug --enable-fast=O0 --disable-shared
$ make
$ cd test
$ make
$ gdb ./test/simple/simple_test
```