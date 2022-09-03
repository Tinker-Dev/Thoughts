# Find a Column

Here the code for finding a column name in a database.

```sql
---------------------------------------
-- Find column
---------------------------------------

DECLARE @OverLinkServer bit
DECLARE @sqlCommand varchar(2000)
DECLARE @SearchServer varchar(255)
DECLARE @SearchDB varchar(255)
DECLARE @FindColumn varchar(255)

SET @OverLinkServer = 1            -- 0 for Local, 1 for LinkedServer
SET @SearchServer =‘Server_Name_Here'    -- Name of LinkedServer if Applicable
SET @SearchDB = 'DB_Name_Here'        -- Name of Database
SET @FindColumn = 'Field_Name_Here'    -- Part or Full Column Name to lookup


IF @OverLinkServer = 1   
    SET @sqlCommand =    'SELECT t.name as TableName, c.name as ColumnName
                        FROM ' + @SearchServer + '.' + @SearchDB + '.sys.columns c 
                        INNER JOIN ' + @SearchServer + '.' + @SearchDB + '.sys.tables t ON c.object_id = t.object_id
                        WHERE c.name like ''%' + @FindColumn +'%'''

ELSE   
    SET @sqlCommand =    'SELECT t.name as TableName, c.name as ColumnName
                        FROM ' + @SearchDB + '.sys.columns c 
                        INNER JOIN ' + @SearchDB + '.sys.tables t ON c.object_id = t.object_id
                        WHERE c.name like ''%' + @FindColumn +'%'''


EXEC (@sqlCommand)

```