# Archive with Dated Location

The only way to fly when it comes to archiving.

  

```
################################ Import ################################

import os
from pathlib import Path
from datetime import date
import shutil

################################ Configuration ################################

# This is your base archive location
conf_archive_share = r''


################################ Functions ################################

# Used to copy files.
def file_copy(def_source, def_destination):
    shutil.copy2(def_source, def_destination)


# Achiving file encase we need them later.
def file_archive(def_source):

    # Creating the full archiving structure.
    date_folder = str(date.today())
    archive_path = Path(conf_archive_share + '\\' + date_folder)

    # Creating the temp archive path if not present.
    if archive_path.is_dir() == False:
        os.makedirs(archive_path)
    else:
        # Nothing to do since the folder is present.
        pass

    # Getting the name of the file from the source path.
    file_name = os.path.basename(def_source)

    # Defining the destination archive path.
    file_dest = str(archive_path) + '\\' + file_name

    # Archiving the files.
    file_copy(def_source, file_dest)

    # Deleting the Source files.
    os.remove(def_source)


```