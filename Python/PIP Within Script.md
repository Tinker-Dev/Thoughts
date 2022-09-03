# PIP Within Script

**Note**: This is a deprecated function.

There are times that you want to make sure that a modules is installed/current before you can proceed with a script. Here is the code to install a module.

  

```
try:
    import pynput
except ImportError:
    from pip._internal import main as pip
    pip(['install', 'pynput', '--user'])
    import pynput

```