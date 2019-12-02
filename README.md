# RISCV Disk Images

This repository contains 32-bit and 64-bit RISC-V Linux disk images and bootloaders to be used with [MARSS-RISCV](https://github.com/bucaps/marss-riscv).

# Prerequisites

- To be able to clone the repository correctly, git-lfs must be
  installed on the system. See
  https://github.com/git-lfs/git-lfs/wiki/Installation for
  distro-specific installation instructions.

- Although the disk images are generic, the kernel + bootloader is
  meant to be used wit [MARSS-RISCV](https://github.com/bucaps/marss-riscv) and temu. See https://bellard.org/temu/
  for more details.

- The disk images need to be decompressed using xz which is part of
  the xz utils package. See https://tukaani.org/xz/ for more details.

- To be able to grow the free space on the disk image, e2fsprogs is
  required. Most Linux distros have this installed by default. See
  http://e2fsprogs.sourceforge.net/ for more details.

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
- Run MARSS-RISCV using the provided config file
  ```
  $ marss-riscv riscvemu.cfg
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

MARSS-RISCV does not propagate file changes to the disk image by default
and discards them upon shutdown. For persistency, pass `-rw` to
marss-riscv on the command line.

# Available Configurations

## riscv32-unknown-linux-gnu

A 32 bit Gentoo linux image cross-compiled using crossdev. Has a
complete toolchain. Everything is compiled with the following flags:

```
CFLAGS="-O2 -pipe -fomit-frame-pointer -g -march=rv32g -mabi=ilp32d"
CXXLAGS="-O2 -pipe -fomit-frame-pointer -g"
```

Currently, gdb isn't functioning properly.
