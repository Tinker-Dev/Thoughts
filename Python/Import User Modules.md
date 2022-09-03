# Import User Modules

Below is the code to used to point the Python toward where user packages are stored.  

  

```
import os
import sys

PathBase = os.getenv(‘APPDATA’)
UserPackagePath = PathBase + ‘\\Python\\Python37\site-packages’
sys.path.insert(0,UserPackagePath)

```