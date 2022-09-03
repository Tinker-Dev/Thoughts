# PyLaunch

Use this applet to run other applets. This one will help you make sure everything is just like you want it to be.  

  

```

############################## Import ##############################

import os
import subprocess
from pathlib import Path
from datetime import date


########################### User Config ###########################

# Pick your version 1, 2, 3, 4
conf_choice = 1

# Full path to the script to run.
conf_script_path = r'Full path to your file here.'


########################## System Config ##########################

conf_option_one_label = 'Python 3.9 32 bit'
conf_option_one_path = r'C:\Program Files (x86)\Python39-32\python.exe'

conf_option_two_label = 'Python 3.9 64 bit'
conf_option_two_path = r'C:\Program Files\Python\Python39\python.exe'

conf_option_three_label = 'Python 3.10 32 bit'
conf_option_three_path = r'C:\Program Files (x86)\Python310-32\python.exe'

conf_option_four_label = 'Python 3.10 64 bit'
conf_option_four_path = r'C:\Program Files\Python310\python.exe'


########################### Functions ###########################

print('')
print('--------------------- Launched with PyLauncher ---------------------')
print('')


# This is the code to run the main applet.
def run_applet():

    if conf_choice == 1:
        print('Running Script With:', conf_option_one_label)
        status = subprocess.Popen([conf_option_one_path, conf_script_path])
    elif conf_choice == 2:
        print('Running Script With:', conf_option_two_label)
        status = subprocess.Popen([conf_option_two_path, conf_script_path])

    elif conf_choice == 3:
        print('Running Script With:', conf_option_three_label)
        status = subprocess.Popen([conf_option_three_path, conf_script_path])

    elif conf_choice == 4:
        print('Running Script With:', conf_option_four_label)
        status = subprocess.Popen([conf_option_four_path, conf_script_path])

    else:
        print('Running Script With:', conf_option_three_label)
        status = subprocess.Popen([conf_option_three_path, conf_script_path])

    print('')
    print('--------------------------------------------------------------------')
    print('')
    status.communicate()


# This is the settings to install the requirements.
def install_requirements(def_req_path):

    if conf_choice == 1:
        print('Installing Requirements With:', conf_option_one_label)
        status = subprocess.Popen([conf_option_one_path, '-m', 'pip', 'install', '--user', '-r', def_req_path])
    elif conf_choice == 2:
        print('Installing Requirements With:', conf_option_two_label)
        status = subprocess.Popen([conf_option_two_path, '-m', 'pip', 'install', '--user', '-r', def_req_path])

    elif conf_choice == 3:
        print('Installing Requirements With:', conf_option_three_label)
        status = subprocess.Popen([conf_option_three_path, '-m', 'pip', 'install', '--user', '-r', def_req_path])

    elif conf_choice == 4:
        print('Installing Requirements With:', conf_option_four_label)
        status = subprocess.Popen([conf_option_four_path, '-m', 'pip', 'install', '--user', '-r', def_req_path])

    else:
        print('Installing Requirements With:', conf_option_three_label)
        status = subprocess.Popen([conf_option_three_path, '-m', 'pip', 'install', '--user', '-r', def_req_path])

    print('')
    print('--------------------------------------------------------------------')
    print('')
    status.communicate()


# Creating the Trigger File.
# If this file is present the process will not try to install requirements.
def create_req_trigger(def_location):

    # Setting the write path.
    req_path = str(def_location) + '\\req.trigger'

    # Writing the file.
    f = open(req_path, "w")
    f.write(str(date.today()))
    f.close()


def requirements_rabbit_hole():
    # Getting the base path from the script path.
    base_location = os.path.dirname(conf_script_path)

    # Scripting the expected path to the requirements.txt file.
    requirements_location = Path(base_location + '\\requirements.txt')

    # Scripting the path to the req.trigger file.
    req_log_location = Path(base_location + '\\req.trigger')

    # Checking to see if the "req.trigger" file is present.
    if req_log_location.is_file() == False:

        # Checking to see if a requirements file is present.
        if requirements_location.is_file() == True:
            install_requirements(str(requirements_location))
            create_req_trigger(base_location)
            run_applet()

        else:
            run_applet()

    else:
        run_applet()


########################### Main Code ###########################

# The line that starts the whole process.
requirements_rabbit_hole()


```