# Setting User Site-Packages Location

There are several reasons why you may install modules as a user and not the system. In this case the modules will be installed in a special location. This code will help flush that out and set it so that the script can use those packages.  

  

```
# These modules are used to work with the OS.
import os
import sys

# This will define the path to the current user's
# Application Data folder.
appdatapath = os.getenv('APPDATA')

# This will look at the running version of Python and
# then break that apart to build out the working path.
versionfolder = ('Python' + str(sys.version_info[0]) + str(sys.version_info[1]))

# This will build out the path to the user's site-packages folder for
# this version of Python.
UserPackagePath = (str(appdatapath) + '\\Python\\' + versionfolder + '\\site-packages')

# This tells the system where the user packages are for this script.
sys.path.insert(0, UserPackagePath)

```