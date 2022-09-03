# File Copy

```

import os
import shutil

# Declaring Global Lists
have_files = []
check_files = []
check_list = []


# This is the path to the files that have already been copied down.
have_file_path = r'C:\Destination'

# This is the source path of file.
check_file_path = r'V:\Source'

# This is where new files will be copied to.
put_file_path = r'C:\Destination

# Getting filenames of the files that we have.
for root, dirs, files in os.walk(have_file_path):
    have_files.append(files)

# Getting filenames of the files at the source.
for root, dirs, files in os.walk(check_file_path):
    check_files.append(files)

# This is fixing the list in a list issue that gets created.
have_list = have_files[0]
check_list = check_files[0]

# Looking for the deltas in the lists.
for filename in have_list:
    if filename in check_list:
        check_list.remove(filename)
    else:
        print('Bad Filename:', filename)


print('Number of New Files:', len(check_list))

filesleft = len(check_list)
for filename in check_list:
    source = check_file_path + '\\' + filename
    destination = put_file_path + '\\' + filename

    print(source, destination)

    shutil.copy(source, destination)

    filesleft = filesleft - 1

    print('Files Left:', filesleft)

check_list.remove(filename)


input("Press any key to terminate the program")

```