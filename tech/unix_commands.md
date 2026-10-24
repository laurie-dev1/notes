## recursively shreds and removes directory contents, use cautiously! 
find directory/ -type f -exec shred -n1 -v -fuz {} \\\;

## fix gpg warnings 
**Set 600 for files:**  
find ~/.gnupg -type f -exec chmod 600 {} \;

**Set 700 for directories:**  
find ~/.gnupg -type d -exec chmod 700 {} \;

## backup and restore windows 11  

**backup**:  
dd if=/dev/sda of=file.mbr bs=512 count=63  
dd if=/dev/sda1 bs=1M status=progress | lz4 > sda1.lz4  
dd if=/dev/sda2 bs=1M status=progress | lz4 > sda2.lz4  
dd if=/dev/sda3 bs=1M status=progress | lz4 > sda3.lz4  
dd if=/dev/sda4 bs=1M status=progress | lz4 > sda4.lz4  

**restore**:  
dd of=/dev/sda if=file.mbr  
unlz4 < sda1.lz4 | dd bs=1M status=progress of=/dev/sda1  
unlz4 < sda2.lz4 | dd bs=1M status=progress of=/dev/sda2  
unlz4 < sda3.lz4 | dd bs=1M status=progress of=/dev/sda3  
unlz4 < sda4.lz4 | dd bs=1M status=progress of=/dev/sda4  

If required do a bootrec /fixmbr from recovery drive  

## rsync full system backup  
rsync -aAXHv --exclude='/dev/*' --exclude='/proc/*' --exclude='/sys/*' \
--exclude='/tmp/*' --exclude='/run/*' --exclude='/mnt/*' \
--exclude='/media/*' --exclude='/lost+found/' / /path/to/backup

## archlinux intel integrated graphics setup for NUC13ANHi7  

sudo pacman -S intel-ucode \
intel-media-driver \
vulkan-tools vulkan-mesa-layers \
vulkan-intel \
intel-gpu-tools \
libva-utils intel-media-sdk

**global env - /etc/environment**  
LIBVA_DRIVERS_PATH=/usr/lib/dri/
LIBVA_DRIVER_NAME=iHD
ANV_DEBUG=video-decode,video-encode

**force intel xe in kernel commandline, /etc/default/grub**  
GRUB_CMDLINE_LINUX="i915.force_probe=!a7a0 xe.force_probe=a7a0"

## grub simple cryptsetup (encrypted root, unecrypted boot).   
GRUB_CMDLINE_LINUX_DEFAULT="cryptdevice=UUID=9accd62c-f3f0-4c26-95f0-2dd5edf6e569:artix rootfs=/dev/mapper/artix"
