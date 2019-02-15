# 4 Build The Basic System Configuration

In this step we will create a basic system configuration that the kernel expects to be present.
It will provide a very basic system that can be enhanced later on.

### 1 Create the remaining basic folder structure

First we create the remaining basic folder structure we know form unix like systems:
```
mkdir -p ${CLFS}/sysroot/{dev,mnt,etc,proc,root,sys,var/{run/,log/,}}
```

We will add a second non-root user called eco so we need a home directory for it as well:
```
mkdir -p ${CLFS}/sysroot/home/eco
```

### 2 Create symlink between /etc/mtab and /proc/mounts
```
ln -svf ../proc/mounts ${CLFS}/sysroot/etc/mtab
```

### 3 Create basic system files

Create password for user root and eco
```
cat > ${CLFS}/sysroot/etc/passwd << "EOF"
root:$1$QQPxxLDJ$8myxaWN23RDrA8z9lUmCT.:0:0:root:/root:/bin/ash
eco:$1$K8QU.Smk$Yme6UbhodChaA.lz2Wubk1:1000:1000:eco:/home/eco:/bin/ash
EOF
```

**NOTE:** The username and password for these users are equal to their name!

Create the basic groups:
```
cat > ${CLFS}/sysroot/etc/group << "EOF"
root::0:
bin::1:
eco::1000:
EOF
```

Create the hostname file:
```
cat > ${CLFS}/sysroot/etc/HOSTNAME << "EOF"
eco32linux
EOF
```

Create the shells file:
```
cat > ${CLFS}/sysroot/etc/shells << "EOF"
/bin/sh
/bin/ash
EOF
```

### 4 Create the init script for Busybox init
```
cat > ${CLFS}/sysroot/etc/inittab << "EOF"
# mount various file systems
::sysinit:mount -t devtmpfs none /dev
::sysinit:mount -t proc none /proc
::sysinit:mount -t sysfs none /sys
::sysinit:mount -o remount,rw /

# start system logging
::sysinit:syslogd
::sysinit:klogd

# set hostname file
::sysinit:/bin/hostname -F /etc/HOSTNAME

# Start an "askfirst" shell on the first terminal
ttyS0::askfirst:-/sbin/getty -L ttyS0 38400 eco32uart

# Start an "askfirst" shell on the first virtual terminal
tty0::askfirst:-/sbin/getty 38400 tty0

# Stuff to do when restarting the init process
null::restart:/sbin/init

# Stuff to do when power off the machine
::shutdown:/bin/umount -a -r
::shutdown:/sbin/swapoff -a
EOF
```

### 5 Create profile and environment script
```
cat > ${CLFS}/sysroot/etc/profile << "EOF"
# Set the initial path
export PATH=/bin:/usr/bin
if [ `id -u` -eq 0 ] ; then
        PATH=/bin:/sbin:/usr/bin:/usr/sbin
        unset HISTFILE
fi

# Setup some environment variables.
export USER=`id -un`
export LOGNAME=$USER
export HOSTNAME=`/bin/hostname`
export HISTSIZE=1000
export HISTFILESIZE=1000
EOF
```

### 6 Create syslog config
```
cat > ${CLFS}/sysroot/etc/syslog.conf << "EOF"
# log everything to /var/log/messages
*.*     /var/log/messages
EOF
```