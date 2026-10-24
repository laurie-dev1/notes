## recursively shreds and removes directory contents, use cautiously! 
find directory/ -type f -exec shred -n1 -v -fuz {} \\\;
