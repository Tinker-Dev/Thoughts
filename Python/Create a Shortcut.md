# Create a Shortcut

Here is the function to create a shortcut. For example to place on on the Desktop.  

  

```
# Bringing in the Needed Items
from win32com.client import Dispatch

# Universal Paths
appdatapath = os.getenv('APPDATA')
desktop = os.path.join(os.path.join(os.environ['USERPROFILE']), 'Desktop')


# Example Values
versionfolder = ('Python' + str(sys.version_info[0]) + str(sys.version_info[1]))
scriptsfolder = (str(appdatapath) + '\\Python\\' + versionfolder + '\\Scripts')
spyderexecutable = (str(appdatapath) + '\\Python\\' + versionfolder + '\\Scripts\\spyder3.exe')
shortcutname = (str(desktop) + '\\Spyder.lnk')


# Creating Shortcut
def CreateShortcut(path, target='', wDir='', icon=''):    
    shell = Dispatch('WScript.Shell')
    shortcut = shell.CreateShortCut(path)
    shortcut.Targetpath = target
    shortcut.WorkingDirectory = wDir
    if icon == '':
        pass
    else:
        shortcut.IconLocation = icon
    shortcut.save()
    

CreateShortcut(shortcutname,spyderexecutable,scriptsfolder,spyderexecutable)

```