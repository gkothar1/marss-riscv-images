# RISCV Disk Images

This repository contains a series of disk images, kernels and
riscvemu configurations for development purposes.

# How to use?

Each directory represents a specific configuration and contains a
userland disk image, bootloader, kernel and riscvemu config file. The
disk images are compressed with xz for storage efficiency. The file
`PACKAGES` lists the packages present in the image.

Follow the instructions below after cloning the repository to get
started:

- cd into the target directory
  ```
  $ cd riscv32-unknown-linux-gnu
  ```
- Decompress the image using xz
  ```
  $ xz -d -k -T 0 riscv32.img.xz
  ```
- Run riscvemu using the provided config file
  ```
  $ riscvemu32 riscvemu.cfg
  ```

Note that the file system on the disk image has almost no space. To
grow the file system follow the steps below:

- Grow the image file to the desired size (1GB in this example):
  ```
  $ truncate --size 1G riscv32.img
  ```
- Attach the disk image to a loopback device
  ```
  # losetup /dev/loop0 riscv32.img
  ```
- Run fsck before growing the file system
  ```
  # e2fsck -f /dev/loop0
  ```
- Grow the file system to its maximum size
  ```
  # resize2fs /dev/loop0
  ```
- Run fsck post resize
  ```
  # e2fsck -f /dev/loop0
  ```
- Detach the loopback device
  ```
  # losetup -d /dev/loop0
  ```

Riscvemu does not propagate file changes to the disk image by default
and discards them upon shutdown. For persistency, pass `-rw` to
riscvemu on the command line.

# Available Configurations

## riscv32-unknown-linux-gnu

A 32 bit Gentoo linux image cross-compiled using crossdev. Has a
complete toolchain. Everything is compiled with the following flags:

```
CFLAGS="-O2 -pipe -fomit-frame-pointer -g -march=rv32g -mabi=ilp32d"
CXXLAGS="-O2 -pipe -fomit-frame-pointer -g"
```

Currently, gdb isn't functioning properly.
