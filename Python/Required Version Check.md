# Required Version Check

This code allows you to quit the script if the right version of Python is not being used.  

```
# This module is used to work with the OS.
import sys

# New Enough Version Check
if sys.version_info<(3,6,0):
  sys.stderr.write("You need Python 3.6 or later to run this script\n")
  # Waits for the user to press "Enter" before
  # closing out the script.
  input("Press Enter to continue...")
  exit(1)

```