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
