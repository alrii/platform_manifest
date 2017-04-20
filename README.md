# Sync Android Source (AOSP)
 * $ repo init -u https://github.com/tab-pi/platform_manifest -b nougat
 * $ repo sync

# Build for Raspberry Pi3
## Build Kernel
 * Install gcc-arm-linux-gnueabihf (For Ubuntu: $ sudo apt install gcc-arm-linux-gnueabihf)
 * $ cd kernel/rpi
 * $ ARCH=arm scripts/kconfig/merge_config.sh arch/arm/configs/bcm2709_defconfig android/configs/android-base.cfg android/configs/android-recommended.cfg
 * $ ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make zImage
 * $ ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make dtbs

## Install python mako module
  sudo apt-get install python-mako

## Build Android source
 * $ source build/envsetup.sh
 * $ lunch rpi3-eng
 * $ make ramdisk systemimage
 
## Help for build failure :
   https://github.com/tab-pi/device_brcm_rpi3/wiki/Build-Errors

## Prepare SD card
 ### Partitions of the card should be set-up like followings.
  1. p1 512MB for BOOT : Do fdisk : W95 FAT32(LBA) & Bootable, mkfs.vfat
  2. p2 512MB for /system : Do fdisk, new primary partition, mkfs.ext4
  3. p3 512MB for /cache  : Do fdisk, mkfs.ext4
  4. p4 remainings for /data : Do fdisk, mkfs.ex4
  5. Set volume label for each partition - system, cache, userdata
  : use -L option of mkfs.ext4, e2label command, or -n option of mkfs.vfat
 
## Write system partition
  * $ cd out/target/product/rpi3
  * $ sudo dd if=system.img of=/dev/<p2> bs=1M
  
## Copy kernel & ramdisk to BOOT partition
  * device/brcm/rpi3/boot/* to p1:/
  * kernel/rpi/arch/arm/boot/zImage to p1:/
  * kernel/rpi/arch/arm/boot/dts/bcm2710-rpi-3-b.dtb to p1:/
  * kernel/rpi/arch/arm/boot/dts/overlays/vc4-kms-v3d.dtbo to p1:/overlays/vc4-kms-v3d.dtbo
  * out/target/product/rpi3/ramdisk.img to p1:/

## HDMI_MODE : If DVI monitor does not work, try followings for p1:/config.txt
hdmi_group=2
  
hdmi_mode=85

### How to set up Android-TV launcher :
  https://github.com/tab-pi/device_brcm_rpi3/wiki#how-to-apply-android-tv-leanback-launcher

Graphics HAL of this build : https://github.com/anholt/mesa/wiki/VC4
