# Services Check

```
$isodate=Get-Date -format s
$isodate=$isodate -replace(":","")
$basepath="C:\ServerPatching\OS_Patching\output"
$outputfile="\ServicesCheck_" + $isodate + ".html"
$outputfilefull = $basepath + $outputfile

$style="" 
$dt = new-object "System.Data.DataTable"
foreach ($svr in get-content "C:\ServerPatching\OS_Patching\serverlist.txt")
{ $svr 
	$cn = new-object System.Data.SqlClient.SqlConnection "server=$svr;database=master;Integrated Security=sspi"
	$cn.Open()
	$sql = $cn.CreateCommand()
	$sql.CommandText = " set nocount on CREATE TABLE #ServicesStatus ( ProcessID int identity(1,1), ServerName nvarchar(100) default @@serverName, ServiceName varchar(100), Status varchar(50), TimeChecked datetime default (getdate()) ) INSERT #ServicesStatus (Status) EXEC xp_servicecontrol N'QUERYSTATE',N'MSSQLServer' update #ServicesStatus set serviceName = 'MSSQLServer' where ProcessID = @@identity INSERT #ServicesStatus (Status) EXEC xp_servicecontrol N'QUERYSTATE',N'SQLServerAGENT' update #ServicesStatus set serviceName = 'SQLServerAGENT' where ProcessID = @@identity INSERT #ServicesStatus (Status) EXEC xp_servicecontrol N'QUERYSTATE',N'msdtc'; update #ServicesStatus set serviceName = 'msdtc' where ProcessID = @@identity; INSERT #ServicesStatus (Status) EXEC xp_servicecontrol N'QUERYSTATE',N'sqlbrowser' update #ServicesStatus set serviceName = 'sqlbrowser' where ProcessID = @@identity select * from #ServicesStatus drop table #ServicesStatus "
	$rdr = $sql.ExecuteReader() 
	$dt.Load($rdr) 
	$cn.Close()
}
$dt| select * -ExcludeProperty RowError, RowState, HasErrors, Name, Table, ItemArray | ConvertTo-Html -head $style -body "<h1>SQL Server Services Status Report</h1>" | Set-Content $outputfilefull


Read-Host -Prompt "Press Enter to continue"

```