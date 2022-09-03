# Is it a Float

Use this if you are needing to figure out if a string is really a float.  

  

```
def is_float(n):
    if n[-2:] == '.0':
        return True
    else:
        return False

```