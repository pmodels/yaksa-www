# Documenting the code
Below is an example of code documentation:
```c
/*!
 * \brief creates a vector layout
 *
 * \param[in]  count        Number of blocks in the vector
 * \param[in]  blocklength  Length of each block
 * \param[in]  stride       Increment from the start of one block to another
 *                          (represented in terms of the count of the oldtype)
 * \param[in]  oldtype      Base datatype forming each element in the vector
 * \param[out] newtype      Final generated type
 */
int yaksa_create_vector(int count, int blocklength, int stride, yaksa_type_t oldtype,
                        yaksa_type_t * newtype);
```
You can find commands for doxygen in [here](http://www.stack.nl/~dimitri/doxygen/manual/commands.html).  Although you can add a brief description in the header file, it is recommended to put detailed code documentation in the source file.

# Generating doxygen documents
Doxygen documents are not automatically generated when Yaksa is built.  When you need doxygen documents, use the following in the top-level directory:
```
$ make doxygen
```
Generated documents will be located in `doc/doxygen`.

# Current snapshot
The following link shows the doxygen html generated from the current git repository.
* <http://www.mpich.org/static/yaksa/doxygen/latest/>