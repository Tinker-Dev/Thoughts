# Create Server Objects List

```
$style="" 
$dt = new-object System.Data.DataTable
	 	 			 
foreach ($svr in get-content "C:\Users\%USERNAME%\Documents\My Docs\Local Server Scripts\Multi Server Scripts\computers.txt")
{ $svr 
        $cn = new-object System.Data.SqlClient.SqlConnection "server=$svr;database=master;Integrated Security=sspi"
        $cn.Open()
        $sql = $cn.CreateCommand()
        $sql.CommandText = " IF OBJECT_ID('tempdb..#SchemaStuff') IS NOT NULL DROP TABLE #SchemaStuff   DECLARE @command1 NVARCHAR(1000);  SET @command1 = (' USE [?] SELECT @@SERVERNAME AS ServerName,DB_NAME() as DatabaseName, name as ObjectName,type_desc AS ObjectType FROM sys.objects WHERE type_desc NOT IN (''SYSTEM_TABLE'',''INTERNAL_TABLE'',''SERVICE_QUEUE'') and DB_Name() NOT IN (''tempdb'',''msdb'') '               );  IF OBJECT_ID('tempdb..#SchemaStuff') IS NULL CREATE TABLE #SchemaStuff ( ServerName nvarchar(200), DatabaseName nvarchar(200), ObjectName nvarchar(200), ObjectType nvarchar(200));  INSERT INTO #SchemaStuff ( ServerName, DatabaseName, ObjectName, ObjectType) EXEC sys.sp_MSforeachdb @command1;  SELECT ServerName, DatabaseName, ObjectName, ObjectType FROM #SchemaStuff; "
        $rdr = $sql.ExecuteReader() 
        $dt.Load($rdr) 
        $cn.Close()
}

$dt | Export-Csv -notypeinformation "C:\Users\%USERNAME%\Documents\My Docs\Local Server Scripts\Multi Server Scripts\Output\DatabaseObjectsInBulk.csv"

#Read-Host -Prompt "Press Enter to continue"

```