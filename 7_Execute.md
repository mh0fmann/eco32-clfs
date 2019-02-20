# 7 Execute The System In The Simulator

Now that everything is build together we can execute the system in the simulator.

### 1 Start the simulator
```
${CLFS}/eco32/build/bin/sim -m 32 -s 2 -t 0 -t 1 -g -d ${CLFS}/disk.img -r ${CLFS}/eco32/build/monitor/simulator/monitor.bin -i
```

In the terinaml press c and enter to start the simulation

### 2 Boot the bootloader

In the ECO32 Terminal 0 enter the following commands to boot Barebox:
```
boot 0
```

Now choose partition 0 to boot:
```
0
```

### 3 Mount the filesystem containing the kernel and the rootfs

Once Barebox is finished booting mount the disk:
```
mount disk0.1
```

Navigate to the boot directory containing the kernel:
```
cd /mnt/disk0.1/boot
```

### 4 Boot the kernel

Set a commandline that will passed to the kernel by Barebox:
```
global linux.bootargs.base="console=ttyS0 root=/dev/hda2"
```

Now we can boot the kernel:
```
bootm uImage
```

**NOTE:** When init runs it may wait for a key press to continue. In this case just press enter on the console to get the boot process going!

### 5 Using the system
Once the kernel finished booting everything should be self explained..
