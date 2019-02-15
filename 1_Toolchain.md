# 1 Build The Cross Toolchain
In this step we will build the toolchain that produces code for the ECO32.
The toolchain build in this step will be used to build the kernel, bootloader and the tools present on the targetfs.

Perform all this steps with extra caution. Doing something wrong here will result in various problems later on!

### 1 Extract and install the Linux headers

Create a folder named 01-linux-headers under ${CLFS}/build and enter it:
```
mkdir -p ${CLFS}/build/01-linux-headers
cd ${CLFS}/build/01-linux-headers
```

Clone the ECO32 Linux port from github:
```
git clone https://github.com/mh0fmann/eco32-linux ./
```

Now we can check and extract/install the linux headers:
```
make ARCH=eco32 headers_check
make ARCH=eco32 INSTALL_HDR_PATH=${CLFS}/sysroot/usr headers_install
```

### 2 Build and install GMP
Create a folder named 02-gmp under ${CLFS}/build and enter it:
```
mkdir -p ${CLFS}/build/02-gmp
cd ${CLFS}/build/02-gmp
```

Extract the downloaded package and enter it:
```
tar -xf ${CLFS}/packages/gmp-6.1.2.tar.bz2
cd gmp-6.1.2
```

Configure the package to meet the required configuration:
```
./configure --prefix=${CLFS}/toolchain --enable-cxx --disable-static
```

Now build and install GMP:
```
make
make install
```

### 3 Build and install MPFR
Create a folder named 03-mpfr under ${CLFS}/build and enter it:
```
mkdir -p ${CLFS}/build/03-mpfr
cd ${CLFS}/build/03-mpfr
```

Extract the downloaded package and enter it:
```
tar -xf ${CLFS}/packages/mpfr-3.1.3.tar.bz2
cd mpfr-3.1.3
```

Configure the package to meet the required configuration:
```
./configure --prefix=${CLFS}/toolchain --disable-static --with-gmp=${CLFS}/toolchain LDFLAGS="-Wl,-rpath,${CLFS}/toolchain/lib"
```

Now build and install MPFR:
```
make
make install
```

### 4 Build and install MPC
Create a folder named 04-mpc under ${CLFS}/build and enter it:
```
mkdir -p ${CLFS}/build/04-mpc
cd ${CLFS}/build/04-mpc
```

Extract the downloaded package and enter it:
```
tar -xf ${CLFS}/packages/mpc-1.0.3.tar.gz
cd mpc-1.0.3
```

Configure the package to meet the required configuration:
```
./configure --prefix=${CLFS}/toolchain --disable-static --with-gmp=${CLFS}/toolchain --with-mpfr=${CLFS}/toolchain LDFLAGS="-Wl,-rpath,${CLFS}/toolchain/lib"
```

Now build and install MPC:
```
make
make install
```

### 5 Build and install ISL
Create a folder named 05-isl under ${CLFS}/build and enter it:
```
mkdir -p ${CLFS}/build/05-isl
cd ${CLFS}/build/05-isl
```

Extract the downloaded package and enter it:
```
tar -xf ${CLFS}/packages/isl-0.15.tar.gz
cd isl-0.15
```

Configure the package to meet the required configuration:
```
./configure --prefix=${CLFS}/toolchain --disable-static --with-gmp-prefix=${CLFS}/toolchain LDFLAGS="-Wl,-rpath,${CLFS}/toolchain/lib"
```

Now build and install ISL:
```
make
make install
```

### 6 Build and install Binutils

Create a folder named 06-binutils under ${CLFS}/build and enter it:
```
mkdir -p ${CLFS}/build/06-binutils
cd ${CLFS}/build/06-binutils
```

Clone the ECO32 Binutils port from github:
```
git clone https://github.com/mh0fmann/eco32-binutils
```

**NOTE:** Do not clone the project directly into the created folder because Binutils recommends building Binutils outside of the source directory.

Configure Binutils to meet the required configuration:
```
eco32-binutils/configure --prefix=${CLFS}/toolchain --target=eco32-unknown-linux-gnu --with-sysroot=${CLFS}/sysroot --with-lib-path=${CLFS}/sysroot/lib --disable-nls --disable-static --disable-multilib --enable-gold=yes --enable-plugins --enable-threads --disable-werror AR=ar AS=as
```

Build and install Binutils
```
make
make install
```

### 7 Build and install GCC stage 1

Create a folder named 07-gcc-1 under ${CLFS}/build and enter it:
```
mkdir -p ${CLFS}/build/07-gcc-1
cd ${CLFS}/build/07-gcc-1
```

Clone the ECO32 GCC port from github:
```
git clone https://github.com/mh0fmann/eco32-gcc
```

**NOTE:** Do not clone the project directly into the created folder because GCC recommends building GCC outside of the source directory.

First we need to make some changes on the GCC sources to make the build process work:
```
export SSPF=eco32-gcc/gcc/config/linux.h
echo -en '\n' >>${SSPF}
echo -en '#undef STANDARD_STARTFILE_PREFIX_1\n' >>${SSPF}
echo -en '#define STANDARD_STARTFILE_PREFIX_1 "/usr/lib/"\n' >>${SSPF}
echo -en '\n' >>${SSPF}
echo -en '#undef STANDARD_STARTFILE_PREFIX_2\n' >>${SSPF}
echo -en '#define STANDARD_STARTFILE_PREFIX_2 ""\n' >>${SSPF}
touch ${CLFS}/sysroot/usr/include/limits.h
```

Configure GCC to meet the required configuration for stage 1:
```
eco32-gcc/configure --prefix=${CLFS}/toolchain --target=eco32-unknown-linux-gnu --with-sysroot=${CLFS} --with-local-prefix=${CLFS}/sysroot/usr/local --with-native-system-header-dir=/usr/include --disable-shared --with-mpfr=${CLFS}/toolchain --with-gmp=${CLFS}/toolchain --with-isl=${CLFS}/toolchain --with-mpc=${CLFS}/toolchain --without-headers --with-newlib --disable-decimal-float --disable-libgomp --disable-libssp --disable-libatomic --disable-libitm --disable-libsanitizer --disable-libquadmath --disable-libvtv --disable-libcilkrts --disable-libstdc++-v3 --disable-threads --disable-multilib --with-system-zlib --enable-languages=c --with-glibc-version=2.22 AR=ar LDFLAGS="-Wl,-rpath,${CLFS}/toolchain/lib"
```

Build and install GCC stage 1
```
make all-gcc all-target-libgcc
make install-gcc install-target-libgcc
```

Delete the dummy limits.h
```
rm -f ${CLFS}/sysroot/usr/include/limits.h
```

### 8 Build and install musl-libc

Create a folder named 08-musl under ${CLFS}/build and enter it:
```
mkdir -p ${CLFS}/build/08-musl
cd ${CLFS}/build/08-musl
```

Clone the ECO32 musl-libc port from github:
```
git clone https://github.com/mh0fmann/eco32-musl.git ./
```

Configure musl-libc to meet the required configuration:
```
./configure CROSS_COMPILE=${CLFS}/toolchain/bin/eco32-unknown-linux-gnu- --prefix=${CLFS}/sysroot --target=eco32 --disable-shared
```

Build and install musl-libc:
```
make
make install
```

Create debug version libc since GCC stage 2 needs it:
```
ln -s ${CLFS}/sysroot/lib/libc.a ${CLFS}/sysroot/lib/libg.a
```

### 9 Build and install GCC stage 2

Create a folder named 09-gcc-2 under ${CLFS}/build and enter it:
```
mkdir -p ${CLFS}/build/09-gcc-2
cd ${CLFS}/build/09-gcc-2
```

Clone the ECO32 GCC port from github:
```
git clone https://github.com/mh0fmann/eco32-gcc
```

**NOTE:** Do not clone the project directly into the created folder because GCC recommends building GCC outside of the source directory.

First we need to make some changes on the GCC sources to make the build process work:
```
export SSPF=eco32-gcc/gcc/config/linux.h
echo -en '\n' >>${SSPF}
echo -en '#undef STANDARD_STARTFILE_PREFIX_1\n' >>${SSPF}
echo -en '#define STANDARD_STARTFILE_PREFIX_1 "/usr/lib/"\n' >>${SSPF}
echo -en '\n' >>${SSPF}
echo -en '#undef STANDARD_STARTFILE_PREFIX_2\n' >>${SSPF}
echo -en '#define STANDARD_STARTFILE_PREFIX_2 ""\n' >>${SSPF}
```

Configure GCC stage 2 to meet the required configuration:
```
eco32-gcc/configure --prefix=${CLFS}/toolchain --target=eco32-unknown-linux-gnu --with-sysroot=${CLFS}/sysroot --with-local-prefix=${CLFS}/sysroot/usr/local --with-native-system-header-dir=/usr/include --disable-nls --disable-shared --enable-languages=c --disable-multilib --with-mpc=${CLFS}/toolchain --with-mpfr=${CLFS}/toolchain --with-gmp=${CLFS}/toolchain --with-isl=${CLFS}/toolchain --with-system-zlib AR=ar LDFLAGS="-Wl,-rpath,${CLFS}/toolchain/lib"
```

Build and install GCC stage 2:
```
make AS_FOR_TARGET="eco32-unknown-linux-gnu-as" LD_FOR_TARGET="eco32-unknown-linux-gnu-ld"
make install
```