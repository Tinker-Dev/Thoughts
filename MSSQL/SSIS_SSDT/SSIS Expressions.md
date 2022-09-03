# SSIS Expressions

Sometimes you have to enter code into SSIS. Here are some bit that can help.  

  

  

## Force a Line Return

Create a derived column called CRLF using the following expression and map it to the final column to create an artificial CRLF in fixed width file.

```
(DT_STR,2,1252)"\r\n"

```

  

* * *

  

## First day of this month

As a DATE:

```
(DT_DATE)((DT_WSTR, 4)YEAR(GETDATE()) + "-" +RIGHT("0" + (DT_WSTR,2)MONTH(GETDATE()),2)+"-01")

```

  

As YYYYMMDD Unicode String:  

```
(DT_WSTR, 4)YEAR(GETDATE()) + RIGHT("0" + (DT_WSTR,2)MONTH(GETDATE()),2)+"01"

```

  

* * *

  

## First day of the previous month

As a DATE:  

```
(DT_DATE)((DT_WSTR, 4)YEAR(DATEADD("mm", -1, GETDATE())) + "-" +RIGHT("0" + (DT_WSTR,2)MONTH(DATEADD("mm", -1, GETDATE())),2)+"-01")

```

  

As YYYYMMDD Unicode String:  

```
(DT_WSTR, 4)YEAR(DATEADD("mm", -1, GETDATE())) + RIGHT("0" + (DT_WSTR,2)MONTH(DATEADD("mm", -1, GETDATE())),2)+"01"

```

  

* * *

  

## Last day of the previous month

```
DATEADD("dd", -1, (DT_DATE)((DT_WSTR, 4)YEAR(GETDATE()) + "-" +RIGHT("0" + (DT_WSTR,2)MONTH(GETDATE()),2)+"-01"))

```

  

* * *

  

## Timestamp in the format YYYYMMDDHHMMSS

(ie 20121217160035)  

  

Using the start time of the containing package:  

```
(DT_WSTR, 4) Year(@[System::StartTime] ) +(Month(@[System::StartTime] )>9?(DT_WSTR, 2) Month(@[System::StartTime] ):"0"+(DT_WSTR, 2) Month(@[System::StartTime] ))+(DT_WSTR, 2) Day(@[System::StartTime] )+SUBSTRING(REPLACE((DT_WSTR, 20)@[System::StartTime], ":", ""), 12, 8)

```

  

Using GETDATE():  

```
(DT_WSTR,4)YEAR(GETDATE()) + RIGHT("0" + (DT_WSTR,2)MONTH(GETDATE()),2) + RIGHT("0" + (DT_WSTR,2)DAY(GETDATE()),2)+SUBSTRING(REPLACE((DT_WSTR, 29)GETDATE(), ":", ""), 12, 6)

```

  

* * *

  

## Value Replace

**Sudo Code:** IF ? True Value : False Value  

```
LEN(Dog) == 1 ? "0.00" : Dog

```

  

* * *