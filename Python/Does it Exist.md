# Does it Exist

It is a common need to check if something is there before you proceed. This script will help you do that.

The script is setup for a folder check, however if you need to check for a file, then you just need to add the file name and figure out what action you want to take if it isn’t present.

  

```
# This module is used to work with the OS.
import os

# Define your folder path.
thepath = 'C:\YourFolder'

# Checking for the directory. If it isn't present we will create it.
if thepath.exists ():
    # If the directory is already present the script will just move on.
    pass
else:
    # If the directory was not present the script will create it.
    os.makedirs(thepath)

```