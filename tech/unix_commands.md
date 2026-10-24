## recursively shreds and removes directory contents, use cautiously! 
find directory/ -type f -exec shred -n1 -v -fuz {} \\\;

## fix gpg warnings 
**Set 600 for files:** 
find ~/.gnupg -type f -exec chmod 600 {} \;

**Set 700 for directories:** 
find ~/.gnupg -type d -exec chmod 700 {} \;
