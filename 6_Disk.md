# 6 Build The Final Disk Image

In this step we will create the final disk image that can be used to boot the system build in this guide

### 1 Create the disk image
```
${CLFS}/eco32/build/bin/mkdisk ${CLFS}/disk.img 50M
```

### 2 Partition the disk image

Copy the disk.part partition configuration file provided in this repository to the clfs directory and navigate to it.

Create the partitions on the disk image:
```
${CLFS}/eco32/build/bin/mkpart ${CLFS}/disk.img ${CLFS}/disk.part
```

### 3 Create the Linux root parition and copy the files to it

Create the root partition image and format it
```
dd if=/dev/zero of=${CLFS}/linux.img bs=512 count=92400
mkfs.ext4 -L "Linux" ${CLFS}/linux.img
```

Mount it and copy all the files to it:
```
sudo mount ${CLFS}/linux.img /mnt
sudo cp -r ${CLFS}/sysroot/* /mnt
sudo umount /mnt
```

### 4 Write the partitions to the disk image

Write the Barebox bootloader to partition 0
```
${CLFS}/eco32/build/bin/wrpart ${CLFS}/disk.img 0 ${CLFS}/build/10-barebox/barebox.pbootblk
```

Write the linux root partition to partition 1
```
${CLFS}/eco32/build/bin/wrpart ${CLFS}/disk.img 1 ${CLFS}/linux.img
```