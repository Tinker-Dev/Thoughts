# Import External Python File as Module

##   

This is the code that you would use to bring in a file that can’t be officially imported as a module. In this example we use a Secrets file that has a username and password in it.

  

```
# Import Local Modules
import importlib.util as ium

# Path to where the secrets file is
secrets_path = r'secrets.py'

# Import the file
spec = ium.spec_from_file_location("secrets", secrets_path)
secrets = ium.module_from_spec(spec)
spec.loader.exec_module(secrets)

# Use secrets
uservalue = secrets.username

print(uservalue)


```