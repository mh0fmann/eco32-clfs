# 2 Build The Barebox Bootloader

In this step we will build the Barebox bootloader which will be used to boot the system.

### 1 Build the Barebox bootloader bundled with a partition bootrecord

Create a folder named 10-barebox under ${CLFS}/build and enter it:
```
mkdir -p ${CLFS}/build/10-barebox
cd ${CLFS}/build/10-barebox
```

Clone the ECO32 Barebox port from github
```
git clone https://github.com/mh0fmann/eco32-barebox ./
```

Create the default configuration which is sufficient for this guide:
```
make ARCH=eco32 defconfig
```

Build the Barebox bundled together with a partition bootrecord:
```
make ARCH=eco32 CROSS_COMPILE=${CLFS}/toolchain/bin/eco32-unknown-linux-musl- barebox.pbootblk
```

Copy mkimage tool to toolchain:
```
cp scripts/mkimage ${CLFS}/toolchain/bin/eco32-unknown-linux-musl-mkimage
```
