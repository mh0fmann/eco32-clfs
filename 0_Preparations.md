# 0 Preparations

Before you start building your custom linux distribution for the ECO32 you need to download and build a various number of packages and tools. Make sure you follow this guide step by step and don't skip any!

### 1. Create the folder structure and base shell variable

Create a folder somewhere on your drive where you have enough space to gather and build everything:
```
mkdir ~/clfs
```

Now we navigate to the folder and create a shell variable the will point to the directory we created so we can use it for upcoming paths.
```
cd ~/clfs
export CLFS=$(pwd)
```
From now on ${CLFS} points to the created directory.

Now it is time to create the basic folder structure for everything we needed during the build process:
```
mkdir -p ${CLFS}/{eco32,toolchain,sysroot,packages,build}
```

### 2. Build the ECO32 Project
First we need to download or clone the ECO32 project from github. It contains a variety of tools we need in order to build the disk image and the simulator to run everything.

Navigate to the newly created eco32 folder inside our clfs directory:
```
cd ${CLFS}/eco32
```

Clone the project:
```
git clone https://github.com/hgeisse/eco32 ./
```

Now we can build the project:
```
make -j 1
```

**NOTE:** It is absolutely necessary to build the project with not more than one thread or the build may fail due to dependencies while building the project. Issuing make with arguments -j 1 solves this issue.


### 3. Download various packages
Now we will download the rest of the packages we will need for the build.
The packages we need during the build are the following:
* GMP 6.1.2
* ISL 0.15
* MPC 1.0.3
* MPFR 3.1.3
* Busybox 1.31.0

Download all these packages to ${CLFS}/packages.

The Kernel, GCC, Binutils and musl-libc are not included here because we will obtain these directly from git.
The ports of these packages are not present in any upstream version. Using the source from git will give use the opportunity to build the latest ports and prevents complex patching of existing sources.

**NOTE:** Make sure to download the correct versions because these are confirmed to work. If you choose any versions other than the ones above it is at your own risk that something may not work!
