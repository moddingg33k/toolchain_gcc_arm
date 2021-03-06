GCC is a great tool-chain for ARM Cortex-M3 architecture embedded projects. It
is a pain to set up if you are stuck on a Windows PC. Mentor Graphics provides
an
[ARM build of GCC.](http://www.mentor.com/embedded-software/sourcery-tools/sourcery-codebench/editions/lite-edition/)
Embedded systems often require customization to details like heap allocation
and C++ exceptions. This project is a bash script for building a GCC
cross-compiler on a [MinGW](http://mingw.org/) host. The compiler targets
arm-non-eabi with the thumb2 instruction set. This configuration is used for
development on the ARM Cortex-M3 architecture.

To use this script:
1. git clone git@github.com:joshuanapoli/arm-none-eabi-gcc.git
2. cd arm-none-eabi-gcc
3. ./build-arm-none-ebai-gcc

By default, the compiler will be installed under /opt/gnu/gcc-4.7.2. You may
want to customize the installation path and versions at the beginning of the
[script](arm-none-eabi-gcc/blob/master/build-arm-none-ebai-gcc). Try restarting
the build if it fails to commit memory from the "cygwin heap".
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              ----------------

First things first, see if it is installed. Write `gcc` in your commandline and double tab. If there
is no file called gcc-4.2 you most likely do not have it. One more check though is to check the gcc
version by doing `gcc -v`. Look at the last line of the output, if it looks like this

```
gcc version 4.2.1 (Based on Apple Inc. build 5658) (LLVM build 2336.11.00)
```

you may think you are in luck and you have gcc-4.2. Unfortunately it is not that simple. This is the
llvm version of gcc-4.2 from Apple and unfortunately does not work with the latest CodeSourcery
packages.

The correct gcc version is easy to install though using homebrew.

```bash
brew tap homebrew/dupes && brew install apple-gcc42
```

and then do

```bash
CC=gcc-4.2 make install-cross
```
###Note:
Homebrew-Dupes also offers a gcc formula which installs, at the time of this writing, GCC 4.7. I have not tried this version myself but might be worth a try since 4.2 is getting pretty dated.

Multilib Build Customization
----------------------------

By default, the toolchain will build with the the multilibs included in the binary builds of G++
Lite. If you want to build multilibs for a larger set of targets similar to the commercial release,
you can build like this:

```bash
FULL_MULTILIBS=true make install-cross
```

*NOTE:* Building with this option will take significantly longer.

Newlib Build Customization
--------------------------

By default, this build enables a number of extra optimizations (most
of which relate to reducing code size) for Newlib by defining the
following:

```bash
CFLAGS_FOR_TARGET="\
 -ffunction-sections -fdata-sections          \ # put code and data into separate sections allowing for link-time
 -DPREFER_SIZE_OVER_SPEED -D__OPTIMIZE_SIZE__ \ # pick simpler, smaller code over larger optimized code
 -Os                                          \ # same as O2, but turns off optimizations that would increase code size
 -fomit-frame-pointer                         \ # don't keep the frame pointer in a register for functions that don't need one
 -fno-unroll-loops                            \ # don't unroll loops
 -D__BUFSIZ__=256                             \ # limit default buffer size to 256 rather than 1024
 -mabi=aapcs"                                 \ # enable use of arm procedure call standard (not sure if this is needed any more)
CCASFLAGS=$(CFLAGS_FOR_TARGET)
```

For an example of what the ```PREFER_SIZE_OVER_SPEED``` and
```__OPTIMIZE_SIZE__``` options do, take a look at the following
[memcpy.c](https://gist.github.com/1636109) extracted from
newlib. Often what one is giving up is manually unrolled loops or
hand-coded assembler that compiles to sizes larger than a simple C
implementation.


If you want something closer to standard options that CodeSourcery
uses simply prepend the make command as follows:

```bash
MATCH_CS=true make install-cross
```

For Newlib this changes the flags to these:

```bash
CFLAGS_FOR_TARGET="-g -O2 -fno-unroll-loops"
```

You can also define your own Newlib flags:

```bash
NEWLIB_FLAGS="-g -O2 -fno-unroll-loops" make install-cross
```

Additionally, there is an option to exclude float support from Newlib functions. At the moment this
should disable float support for IO functions:

```bash
NEWLIB_NOFLOAT=true make install-cross
```

Extras From Binary Distribution
-------------------------------

Some of the CodeSourcery CS3 libraries are distributed with G++ Lite,
but the sources for these are not made available, nor are the
licensing terms in the binary release of G++ Lite permissive of my
including a small compressed download of these libraries with this
build file.  However, I have added a make target that should be able
to pull down the binary Linux tarball extract these libraries and a
few extras, and place them into the correct directories.  To use this,
type the following *after* you have installed your toolchain:

```bash
make install-bin-extras
```

If you need the binary extras installed at a specific prefix, you can
use the following style of incantation:

```bash
PREFIX=/some/other/location make install-bin-extras
```

So, if you had placed your pre-built binaries at
/usr/local/arm-cs-tools, you could use the following:

```bash
PREFIX=/usr/local/arm-cs-tools make install-bin-extras
```

NOTE: use of these libraries is untested by the creator of the
Makefile.  It seemed simple enough to add this after a user had
mentioned a desire to have these libraries available.


Special Thanks
--------------

 * Rob Emanuele for the basis of this
   [Makefile](http://elua-development.2368040.n2.nabble.com/Building-GCC-for-Cortex-td2421927.html)
   as a starting point.

 * Liviu Ionescu for numerous comments suggestions/suggestions and fixes.
