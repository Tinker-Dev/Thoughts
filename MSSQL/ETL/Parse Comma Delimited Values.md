# Parse Comma Delimited Values

- This method is usable for up to 1000 values.
- Because 1000 is the max limit of a row value expression. Or till the limit of the varchar for the @InsertStatement is reached.
- Since this method uses EXEC, be wary of code injection and don't use it for strings based on unverified user input.

  

```
IF OBJECT_ID('tempdb..#tempTable') IS NOT NULL DROP TABLE #tempTable;
CREATE TABLE #tempTable (number int);

DECLARE @TEXT varchar(max) = '1,17,25,44,46,67,88';

DECLARE @InsertStatement varchar(max) = 'insert into #tempTable values ('+REPLACE(@TEXT,',','),(')+');';
EXEC (@InsertStatement);

SELECT * FROM #tempTable;

```