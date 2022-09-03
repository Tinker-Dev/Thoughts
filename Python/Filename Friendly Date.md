# Filename Friendly Date

There are plenty of times that you might want to have a date added to a file name. This is one of many ways to do that.  

```
# This module is so that we can read the time of the machine.
import datetime

# Breaking down the date and time. 
ProcessTime = datetime.datetime.now()
P_Mon = str(ProcessTime.month)
P_Day = str(ProcessTime.day)
P_Year = str(ProcessTime.year)
P_Hour = str(ProcessTime.hour)
P_Min = str(ProcessTime.minute)
P_Sec = str(ProcessTime.second)
FileNameFriendlyDate = (P_Mon +'-'+ P_Day +'-'+ P_Year +'_'+ P_Hour +'-'+ P_Min +'-'+ P_Sec)

# Output what we have created.
print(FileNameFriendlyDate)

```