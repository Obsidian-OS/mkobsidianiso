# `mkobsidianiso`

a script designed to generate bootable live ISO images from a provided [`mkobsidiansfs`](https://github.com/Obsidian-OS/mkobsidiansfs)-created system image.

## Overview

This script streamlines the creation of live ISOs, enabling users to package a complete system into a portable, bootable image. It is particularly useful for packing weird distros like Void which need Void, and for several other purposes.

## Usage

To create a live ISO, `mkobsidianiso` requires a configuration file, the path to your root filesystem (which can be either a directory or a squashfs system image), and the desired output path for the ISO file.

```bash
mkobsidianiso [OPTIONS] <config> <rootfs> <output.iso>
```

### Arguments

*   `<config>`: Path to the configuration file.
*   `<rootfs>`: The source root filesystem (a directory or a squashfs system image).
*   `<output.iso>`: The path where the generated ISO file will be saved.

### Options

*   `-k, --kernel <version>`: Specify the kernel version to use. If not provided, the script attempts to auto-detect it from the root filesystem.
*   `-h, --help`: Display the help message and exit.
*   `-v, --version`: Display the script version and exit.

### Configuration File Options

The `<config>` file is sourced by the script and can define the following variables:

*   `DISTRO`: The distribution name displayed in the GRUB menu.
*   `ISO_NAME`: The product name for the ISO file.
*   `ISO_VERSION`: The product version for the ISO file.
*   `EXTRA_GRUB_CONFIG`: Additional GRUB menu entries or configuration.
*   `BUSYBOX`: Path to the BusyBox binary.

### Example

```bash
# Create an ISO using a configuration file, a rootfs directory, and output to obsidian.iso
mkobsidianiso myconfig.conf /path/to/your/rootfs obsidian.iso

# Create an ISO with a specific kernel version from a squashfs image
mkobsidianiso myconfig.conf -k 6.12.62_1 /path/to/rootfs.sfs custom_obsidian.iso
```

## Dependencies

The script relies on the following tools, which must be installed on your system:

*   `mksquashfs` (from `squashfs-tools`)
*   `grub-mkrescue` (from `grub`)
*   `unzstd` (from `zstd`)
*   `busybox` (or `busybox-static`)

## How it Works

1.  **Initialization**: Sets up a temporary working directory and handles cleanup on exit.
2.  **Kernel Detection**: Identifies or uses a specified kernel version from the root filesystem.
3.  **SquashFS Creation (if necessary)**: If a directory is provided as the root filesystem, it is converted into a compressed squashfs image (`system.sfs`).
4.  **Module Extraction**: Extracts critical kernel modules (`squashfs.ko`, `loop.ko`, `overlay.ko`) required for the live environment.
5.  **BusyBox Integration**: Copies the BusyBox binary into the ISO's sbin directory for essential utilities.
6.  **Init Script Generation**: Creates a custom `init` script responsible for mounting the squashfs, setting up an overlay filesystem, and pivoting the root to the live system.
7.  **GRUB Configuration**: Generates `grub.cfg` to enable booting the live ISO.
8.  **ISO Assembly**: Utilizes `grub-mkrescue` to compile all components into the final bootable ISO image.

## License

This project is licensed under the [MIT License](LICENSE).
