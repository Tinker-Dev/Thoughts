# Update the Last Updated Date on a Table

From time to time there is an need to fake that a set of tables have been updated. Here are a set of cursor (I know) scripts that will help you get that done. Keep in mind this is setup to do all of the tables in the chosen database.  

  

## Check the current sent of dates on the tables.

```
SELECT   a.Name as 'Table_Name'
		 ,CAST(modify_date as date) as 'Last_Updated' 
FROM     sys.objects A 
         INNER JOIN sys.schemas B 
           ON A.SCHEMA_ID = B.SCHEMA_ID 
WHERE    TYPE = 'U'
ORDER BY a.name

```

  

## Add a Column to the Tables

```
SET NOCOUNT ON 
 
DECLARE @schema sysname 
DECLARE @TABLE sysname 
DECLARE @sqlCommand VARCHAR(8000) 
DECLARE @object_id INT 
  
 
DECLARE TAB_CURSOR CURSOR  FOR 
SELECT   B.NAME      AS SCHEMANAME, 
         A.NAME      AS TABLENAME, 
         A.OBJECT_ID 
FROM     sys.objects A 
         INNER JOIN sys.schemas B 
           ON A.SCHEMA_ID = B.SCHEMA_ID 
WHERE    TYPE = 'U'
ORDER BY 1 
 
OPEN TAB_CURSOR 
 
FETCH NEXT FROM TAB_CURSOR 
INTO @schema, 
     @TABLE, 
     @object_id 
 
WHILE @@FETCH_STATUS = 0 
  BEGIN 
  
    SET @sqlCommand = 'ALTER TABLE ' + @schema + '.' + @TABLE + ' ADD WUMPUS varchar(1)' 
  
    EXEC(@sqlCommand) 

    FETCH NEXT FROM TAB_CURSOR 
    INTO @schema, 
         @TABLE, 
         @object_id 
  END 
 
CLOSE TAB_CURSOR 
DEALLOCATE TAB_CURSOR

```