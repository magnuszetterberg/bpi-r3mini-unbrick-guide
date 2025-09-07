# Banana Pi R3 Mini OpenWRT Rescue Guide

## Prerequisites

Before you begin, make sure you have:

- **1 USB stick** (formatted as FAT32)
- **1 USB-to-Serial cable** connected to the internal serial port (USB-C does not seem to work)
- **OpenWRT files** downloaded from: [OpenWRT 24.10.2 for Mediatek Filogic](https://downloads.openwrt.org/releases/24.10.2/targets/mediatek/filogic/)

- **mtk_uartboot binary** downloaded from: [mtk_uartboot GitHub](https://github.com/981213/mtk_uartboot)

- **RAM .bin files** (`bpi-r3mini_ram_bl2.bin` and `bpi-r3mini_ram_fip.bin`) downloaded from the [fw-web.de BPI-R3 Mini Wiki](https://wiki.fw-web.de/doku.php?id=en:bpi-r3mini:start#fix_bricked_boot) and placed in the same folder as the mtk_uartboot binary

This guide helps you recover a borked Banana Pi R3 Mini device and restore OpenWRT using UART boot and USB stick.

---

## 1. Boot Device via UART

```bash
./mtk_uartboot -s /dev/ttyUSB0 --aarch64 --payload ./bpi-r3mini_ram_bl2.bin --fip ./bpi-r3mini_ram_fip.bin
```

---

## 2. Boot Initramfs Recovery OS from USB

In U-Boot, run:

```bash
usb start
mmc dev 0
load usb 0:1 $loadaddr openwrt-24.10.2-mediatek-filogic-bananapi_bpi-r3-mini-initramfs-recovery.itb
bootm $loadaddr
```

---

## 3. Enable USB Stick Support (OpenWRT)

```bash
opkg update
opkg install kmod-usb-core kmod-usb2 kmod-usb-ohci kmod-usb-storage
opkg install kmod-fs-vfat kmod-fs-ext4 kmod-fs-exfat block-mount fdisk
```

---

## 4. Mount USB Stick

```bash
mkdir -p /mnt/usb
mount -t vfat /dev/sda1 /mnt/usb
```

---

## 5. Write GPT Partition Table

```bash
dd if=/mnt/usb/openwrt-24.10.2-mediatek-filogic-bananapi_bpi-r3-mini-emmc-gpt.bin of=/dev/mmcblk0
# Optionally sync here
reboot  # reload partition table
```

---

## 6. Reboot & Boot Recovery OS Again

Repeat step 1 and 2 to boot recovery OS again.

---

## 7. Install USB Support Again (if needed)

Repeat step 3 if required.

---

## 8. Mount USB Stick Again

Repeat step 4 if required.

---

## 9. Write Bootloader & Images to Partitions

```bash
echo 0 > /sys/block/mmcblk0boot0/force_ro
dd if=/mnt/usb/openwrt-24.10.2-mediatek-filogic-bananapi_bpi-r3-mini-emmc-preloader.bin of=/dev/mmcblk0boot0
dd if=/mnt/usb/openwrt-24.10.2-mediatek-filogic-bananapi_bpi-r3-mini-emmc-bl31-uboot.fip of=/dev/mmcblk0p3
dd if=/mnt/usb/openwrt-24.10.2-mediatek-filogic-bananapi_bpi-r3-mini-initramfs-recovery.itb of=/dev/mmcblk0p4
```

---


---

## Acknowledgements

Special thanks to the guide at [fw-web.de BPI-R3 Mini Wiki](https://wiki.fw-web.de/doku.php?id=en:bpi-r3mini:start#fix_bricked_boot) for invaluable help in fixing bricked devices.
