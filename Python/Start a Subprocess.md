# Start a Subprocess

From time to time, you will have a need to call another file from inside a Python script. This is the script that you can use to do that.  

  

```
# Bring in the needed Modules
import subprocess
    
# Call the other script
create = [sys.executable, '.\\setcreds.py']
subprocess.call(create)

```