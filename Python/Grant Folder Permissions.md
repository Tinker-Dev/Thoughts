# Grant Folder Permissions

**Warning:** Be careful with this code, as you can lose permissions to folders if you don’t set things right.::

When you need to create a folder and set the permissions to be something other than what it would default to.

  

```
# Basic OS Functions
import os

# For working with Windows Security
import win32security
import ntsecuritycon as con

# Working with Usernames and Passwords
import getpass

# Define the path to the current user's folder.
profilefolder = os.path.join(os.path.join(os.environ['USERPROFILE']))

# Pulling current user's username
pulleduser = getpass.getuser()

# Defining parameters to be used in function call.
foldername = profilefolder + '\\UserTest'
user = pulleduser
permission = 'Full'


# This is the function that is used to actual grant the access.
def GrantAccessToFolder(FOLDERNAME,USER,PERMISSION):       
        # Create the folder if it is missing then add the permissions.
        if not os.path.exists(FOLDERNAME):
            os.makedirs(FOLDERNAME) 
        # Declaring the initial creation variables.    
        UserOrGroup, domain, type = win32security.LookupAccountName ("", USER)
        # Getting the SID for the user or group
        sd = win32security.GetFileSecurity(FOLDERNAME, win32security.DACL_SECURITY_INFORMATION)
        dacl = sd.GetSecurityDescriptorDacl()
        # Declaring the level of access that is to be given to the user       
        if (PERMISSION == "Read"): 
            # Declaring Read Permissions based on key word in sheet
            dacl.AddAccessAllowedAce(win32security.ACL_REVISION, con.FILE_GENERIC_READ, UserOrGroup)
            # Applying the chosen level of access
            sd.SetSecurityDescriptorDacl(1, dacl, 0)
            win32security.SetFileSecurity(FOLDERNAME, win32security.DACL_SECURITY_INFORMATION, sd)
        elif (PERMISSION == "Write"):
            # Setting Read/Write Permissions based on key word in sheet.   
            dacl.AddAccessAllowedAce(win32security.ACL_REVISION, con.FILE_GENERIC_READ | con.FILE_GENERIC_WRITE, UserOrGroup)
            # Applying the chosen level of access
            sd.SetSecurityDescriptorDacl(1, dacl, 0)
            win32security.SetFileSecurity(FOLDERNAME, win32security.DACL_SECURITY_INFORMATION, sd)
        elif (PERMISSION == "Full"):
            # Setting Full Permissions based on key word in sheet.   
            dacl.AddAccessAllowedAce(win32security.ACL_REVISION, con.FILE_ALL_ACCESS, UserOrGroup)
            # Applying the chosen level of access
            sd.SetSecurityDescriptorDacl(1, dacl, 0)
            win32security.SetFileSecurity(FOLDERNAME, win32security.DACL_SECURITY_INFORMATION, sd)
        else:
            print("A permission hasn't been set for this user: ", UserOrGroup)
                     

# Calling the function           
GrantAccessToFolder(foldername,user,permission)

```