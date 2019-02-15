# 3 Build The Target Tools

In this step we will build all the target tools that will be present on the Linux system.
In this guide we will use sorley Busybox to provide all applications available to the system.
If you need any further applications and tools you need to build an provide them yourself.

### 1 Build and install Busybox

Create a folder named 11-busybox under ${CLFS}/build and enter it:
```
mkdir -p ${CLFS}/build/11-busybox
cd ${CLFS}/build/11-busybox
```

Extract the downloaded package and enter it:
```
tar -xf ${CLFS}/packages/busybox-1.28.2.tar.bz2
cd busybox-1.28.2
```

Copy the config file busybox.config from this repository to the top-level source directory of Busybox in ${CLFS}/build/11-busybox/busybox-1.28.2 and rename it to .config

Now build busybox with the configuration provided:
```
make
```

Now we install Busybox on the target sysroot:
```
make CONFIG_PREFIX=${CLFS}/sysroot install
```

Busybox needs to have its SUID bit set on its core binary in order be executed by users that are not root.
```
chmod u+s ${CLFS}/sysroot/bin/busybox