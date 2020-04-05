# URLs
* SSH URL: `git@github.com:pmodels/yaksa.git`
* HTTPS URL: `https://github.com/pmodels/yaksa.git`

For differences between above URLs, please refer to https://help.github.com/articles/which-remote-url-should-i-use/

# Checking out the Yaksa source
To check out a new copy of the Yaksa source, use 

    $ git clone --origin yaksa git@github.com:pmodels/yaksa.git yaksa

or 

    $ git clone --origin yaksa https://github.com/pmodels/yaksa.git yaksa

This will create a yaksa directory in your `$PWD` that contains a completely functional repository with full project history.

You can see the branches it contains using:

    $ git branch -a
    * master
      remotes/yaksa/HEAD -> yaksa/master
      remotes/yaksa/master

# Setting up the build environment
The git repository does not contain any of the "derived" files, including `configure` scripts and `Makefile`s. To build these, run

    $ ./autogen.sh

Occasionally changes are made to the autoconf macros that are not detected by the dependency tests for the `configure` scripts.  It is always correct to delete all the `configure` script before running `autogen.sh`:

    $ find . -name configure -print | xargs rm 
    $ ./autogen.sh

The autoconf macros and the `configure.ac` scripts require the following:
* [autoconf](http://www.gnu.org/software/autoconf/) version 2.67 (or higher)
* [automake](http://www.gnu.org/software/automake/) version 1.12.3 (or higher)
* [GNU libtool](http://www.gnu.org/software/libtool/libtool.html) version 2.4 (or higher) 

You can select a particular version of autoconf and autoheader by using the environment variables `AUTOCONF` and `AUTOHEADER` respectively.  `autogen.sh` will use these if they are set.  However, note that for these tools to work properly, both they and all of their data files must be installed in the same set of directories.  The easiest way to ensure this is to use exactly the same configure arguments when you configure and install these tools. For example, if you set the prefix, set the **prefix** to exactly the same path for all three tools.

# Building the software
After setting up the build environment with `autogen.sh`, you can perform the usual three step process to build it like any other unix package:

    $ ./configure --prefix=INSTALLATION_PREFIX 
    $ make -j 
    $ make -j install

Obviously, substitute `INSTALLATION_PREFIX` above with a proper directory. Otherwise `/usr` will be assumed as a default.

# Updated derived files such as configure
If you change one of the files that is the source for a derived file, such as a `configure.ac` file, you will need to rebuild the derived file (e.g., the corresponding `configure` file).  The safest way to do this is to rerun `autogen.sh`:

    $ ./autogen.sh

# Cleaning everything
If you want to remove all untracked files from your current git branch, use

    $ git clean -x -d -f
