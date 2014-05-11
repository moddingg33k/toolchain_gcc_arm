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
