# 5 Build The Linux Kernel

In this step we will finally build the kernel itself.

### 1 Build the Linux kernel
Create a folder named 12-linux under ${CLFS}/build and enter it:
```
mkdir -p ${CLFS}/build/12-linux
cd ${CLFS}/build/12-linux
```

Clone the ECO32 Linux port from github:
```
git clone https://github.com/mh0fmann/eco32-linux ./
```

Create the default configuration which is sufficient for this guide:
```
make ARCH=eco32 defconfig
```

Now build the kernel and create a Barebox compatible uImage:
```
make ARCH=eco32 CORSS_COMPILE=${CLFS}/toolchain/bin/eco32-unknown-linux-musl- uImage
```

### 2 Copy the kernel to the sysroot
```
mkdir -p {CLFS}/sysroot/boot
cp arch/eco32/boot/uImage ${CLFS}/sysroot/boot/
```
