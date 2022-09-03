# Logging

Not all scripts are going to be ran in the console so that the errors are easily view able. In addition you may not be the person running the script but will need to know various details that the user may not note. Logging gives you an ability to know what is going on.

There is a small _smart_ function built into this logging process. You can set the _maxbytes_ of the log file. Once this max is hit the function will start writing to a new file. This keeps from having super large unreadable logs. You can also set how many logs to keep.

  

## Code:

```
# Import what is needed.
import logging
from logging.handlers import RotatingFileHandler


# Where would you like to LOG to?
LogPath = ".\\status.log"


# Setting up the handling and timestamp
logger = logging.getLogger(__name__)
logger.setLevel(logging.INFO)
handler = RotatingFileHandler(LogPath, mode='a', maxBytes=50*1024, backupCount=1, encoding=None, delay=0)
handler.setLevel(logging.INFO)
formatter = logging.Formatter('%(asctime)s - %(message)s')
handler.setFormatter(formatter)
logger.addHandler(handler)


fakeerror = 'Divide by zero error encountered.'


logger.info('----------------------------------------------------------')
logger.info(fakeerror)

```

  

## Log:

```
2020-01-27 11:10:03,386 - ----------------------------------------------------------
2020-01-27 11:10:03,387 - Divide by zero error encountered.

```