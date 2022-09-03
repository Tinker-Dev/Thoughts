# Reading an INI file

There are many reasons that you would want to read from an INI file. The main one would be for configuration details or parameters.  

  

## Code:

```
# Bringing in the modules that are needed.
import os
from configparser import ConfigParser


# This is the path to the INI file. In this case it is
# in the same folder as the script.
INIPATH =  ".\config.ini"


# Checking for the INI file's presence.          
if not os.path.exists(INIPATH):
    # Draw Messagebox
    # Hiding an inert window that is created by tkinker
    GUIShell = tk.Tk()
    GUIShell.withdraw()
    # The \n\n in the message below is a double line
    # break. One of them would only single break the line.
    messagebox.showerror("Missing File",'The "config.ini" file is missing.\n\nIt should be in the script directory.')
else:
    # Reading the various configurations from the INI file.
    config = ConfigParser()
    config.read(INIPATH)
    iniuser = config.get('LOGIN', 'USERNAME')
    inipass = config.get('LOGIN', 'PASSWORD')

    loginstring = ('Username: ' + iniuser + '\nPassword: ' + inipass)

    messagebox.showinfo("Login Details",loginstring)

```

  

  

## The INI Layout:

```
[LOGIN]

USERNAME = John.Doe

PASSWORD = 34f5t2348sgvw56

```