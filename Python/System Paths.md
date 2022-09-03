# System Paths

There are universal places that you will need to get to in Windows. The last thing that you want to do is figure out the Username of the current user and then build paths around that. The keep from having to do that there are some universal paths that you can take care of.  

  

```
# These modules are used to work with the OS.
import os
import pathlib

# Application Data
appdatapath = os.getenv('APPDATA')

# User Desktop
desktop = os.path.join(os.path.join(os.environ['USERPROFILE']), 'Desktop')

```