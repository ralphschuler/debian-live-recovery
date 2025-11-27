# Debian Recovery Live USB

This repository provides a way to build a customised Debian live system for data recovery and administrative tasks. It targets the current stable Debian release (Trixie, Debian 13)【335699653578159†L26-L29】 and supports both `amd64` and `i386` architectures, ensuring compatibility with very old PCs and 32-bit EFI firmware.

## Contents

The live system includes a curated set of tools for file-system repair, disk imaging and network analysis. Key packages are **GParted**, **fsarchiver**, **GNU ddrescue**, **TestDisk** and **Photorec**, as well as NTFS-3G, `memtest86+`, `rsync`, LVM2 and cryptsetup. For hardware monitoring and convenience it also ships `smartmontools`, `lsblk`, `lshw`, **Midnight Commander** and editors such as `nano` and `vim`. Additional network utilities like `nmap`, `tcpdump` and `iperf3` are included【902333962684394†L45-L57】【902333962684394†L103-L124】., along with extra file system and RAID utilities (`btrfs-progs`, `zfsutils-linux`, `mdadm`) and the forensic suite `sleuthkit`

## Build process

The project uses the **live-build** tool. The `auto/config` script sets up a build configuration for the selected architecture (default is `amd64`), activates `contrib`, `non-free` and `non-free-firmware` repositories, and requests ISO-hybrid images. Running `lb build` then produces the ISO image according to this configuration【843226263871971†L2105-L2141】【843226263871971†L2143-L2147】 Use the `auto/build` script to run `lb build` and handle logging and ISO renaming automatically..

### Prerequisites

On a Debian or Ubuntu host install live-build and optional tools:

```bash
sudo apt-get update
sudo apt-get install live-build grub-efi-ia32-bin xorriso squashfs-tools
```

The additional packages are used to inject a 32-bit EFI bootloader into the final image (see below).

### Using the auto scripts

The recommended way to build images is via the scripts in the `auto` directory【552664018867803†L52-L60】:

```bash
# Clean previous builds (removes build artefacts and cached variables)
./auto/clean

# Configure for amd64 and build
ARCH=amd64 ./auto/config
sudo ./auto/build

# Configure for i386 and build
ARCH=i386 ./auto/config
sudo ./auto/build
```

The `auto/build` script wraps `lb build`, writes its log to `build.log` and renames the resulting file to `debian-live-recovery-${ARCH}.iso`. Use `auto/clean` between builds to remove previous configuration and build artefacts.

#### Writing the ISO to a USB stick

Hybrid ISOs can be written directly to a USB stick (replace `/dev/sdX` with the correct device):

```bash
sudo cp debian-live-recovery-amd64.iso /dev/sdX && sync
```

All data on the stick will be overwritten【843226263871971†L1433-L1447】. To use unused space on the stick, create a second partition with **GParted** or **parted**【843226263871971†L1451-L1484】.

## Support for 32-bit EFI

Many older computers, especially some Core 2 Duo Macs, have only a 32-bit EFI bootloader although they can run 64-bit kernels【8635060695767672†L14-L18】. To boot the 64-bit ISO on such devices, copy the file `bootia32.efi` into the `EFI/BOOT` directory of the ISO【8635060695767672†L133-L144】. The package `grub-efi-ia32-bin` provides this file. Our workflow automatically injects it into the AMD64 ISO.

Alternatively, you can place both ISOs on a Ventoy stick. Ventoy automatically selects the appropriate image and allows booting on both 32- and 64-bit systems.

## Automatic releases

The `.github/workflows` directory contains a GitHub Actions configuration. On every change to the `main` branch a workflow builds the ISOs for `amd64` and `i386`, injects `bootia32.efi` into the AMD64 image and uploads both as release assets. You can adjust the workflow to create or upload GitHub releases or integrate additional steps.

## License

This project is licensed under the GNU General Public License v3 or later.
