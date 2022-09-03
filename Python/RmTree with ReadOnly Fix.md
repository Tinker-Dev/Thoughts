# RmTree with ReadOnly Fix

The code below will delete a chosen folder. If that folder has ReadOnly objects in it , they will be taken care of and deleted as well. This is a cascade delete, so don’t point this at the drive root or you will have a bad day.  

  

```

import os
import stat
import shutil

Del_Path = ‘PATH TO FOLDER TO DELETE’

def remove_readonly(func, path, _)
	os.chmod(path, stat.S_IWRITE)
	func(path)

if os.path.exists(Del_Path);
	shutil.rmtree(Del_Path, onerror=remove_readonly)
else:
	None

```