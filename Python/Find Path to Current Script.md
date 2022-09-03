# Find Path to Current Script

Some times you need to know the path to the current executed script, the main script just encase you are nesting scripts. For example if you are using a config file to drive parameters in you script you would generally have that file right next to your script. So you would need to have that script locate the config file in order to read it.  

  

```
# Imports
import os

# Getting the location of this Script.
scriptdir = os.path.dirname(os.path.abspath(__file__))

# Adding the script to that PATH on the
# machines so that user modules can be found.
sys.path.append(scriptdir)

```