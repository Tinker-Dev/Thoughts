# Table Life Refresh

From time to time you will find yourself in a place where and Table will be deleted if it isn't modified in a given amount of time. The following scripts should help you manage an entire database once.  

  

## Step 1 - Bulk ADD a Column

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

  

## Step 2 - Bulk Remove a Column

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
  
    SET @sqlCommand = 'ALTER TABLE ' + @schema + '.' + @TABLE + ' DROP COLUMN WUMPUS' 
  
    EXEC(@sqlCommand) 

    FETCH NEXT FROM TAB_CURSOR 
    INTO @schema, 
         @TABLE, 
         @object_id 
  END 
 
CLOSE TAB_CURSOR 
DEALLOCATE TAB_CURSOR

```