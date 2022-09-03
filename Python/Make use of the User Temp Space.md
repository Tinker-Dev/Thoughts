# Make use of the User Temp Space

Use this if you would like to be able to use the temp space in Windows.  

  

```
####################### Imports #######################
import os
from pathlib import Path

####################### User Configuration #######################

# This will be used to drive the temp space folder name.
conf_project_name = 'YOUR_PROJECT'

####################### Functions #######################
# This is just making sure that the process has a temp location to write files to.
def user_temp_loc():
    # Defining temporary file path.
    userhome = str(Path.home())
    temp_loc = userhome + '\\AppData\\Local\\Temp\\' + conf_project_name
    temp_path = Path(temp_loc)

    # Creating the temp file path if not present.
    if temp_path.is_dir() == False:
        os.makedirs(temp_loc)
    else:
        # Nothing to do since the folder is present.
        pass

    return str(temp_path)


```