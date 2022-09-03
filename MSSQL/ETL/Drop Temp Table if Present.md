# Drop Temp Table if Present

Here is a bit of code that checks to see if a table is present before trying to drop it. This allows you to keep things cleaned up without kicking out errors from time to time.  

  

## Pre SQL Server 2016

### Version 1

```
IF OBJECT_ID('tempdb..#YourTable') IS NOT NULL 
BEGIN 
    DROP TABLE #YourTable
END

```

  

### Version 2

```
IF OBJECT_ID('tempdb..#MyTemp') IS NOT NULL DROP TABLE #MyTemp

```

  

  

## 2016 and Beyond

```
DROP TABLE IF EXISTS #YourTable

```