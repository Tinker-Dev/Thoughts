# Redirect GIT Path

This would be used if you were going to include the GIT program files in your distribution of an application and not have to worry about GIT being installed.

  

This would be how you might start things off it you were using “GitPython”.

```

import os

# This is to point to the execution folder.
Script_Path = os.path.dirname(os.path.abspath(__file__)); 

# This is going to redirect Python to use the embedded version of GIT.
Git_Path = Script_Path + ‘\\git\bin\\git.exe’
os.environ[‘GIT_PYTHON_GIT_EXECUTABLE’} = Git_Path

# Now that you have pointed the system to where GIT is you can import
# the modules that will look for it.

import git

```