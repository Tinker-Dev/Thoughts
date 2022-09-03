# Active Version Path

Sometimes you need to call an item that have been published in a sub folder of your Python install. If the PATH to your Python install is not in the Environment Variables it can be trouble some to nail this down. Below is code to help with this.  

  

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

# This will build out the path to the executable for Spyder. 
spyderexecutable = (str(appdatapath) + '\\Python\\' + versionfolder + '\\Scripts\\spyder3.exe')

# This will print the path.
print (spyderexecutable)

```