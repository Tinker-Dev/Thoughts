# Read from a Database

```
$isodate=Get-Date -format s
$isodate=$isodate -replace(":","")
$basepath="C:\ServerPatching\OS_Patching\output"
$outputfile="\LiteSpeedVersion_" + $isodate + ".html"
$outputfilefull = $basepath + $outputfile

$style=""
$dt = new-object "System.Data.DataTable"
foreach ($svr in get-content "C:\ServerPatching\OS_Patching\serverlist.txt")
{ $svr
	$cn = new-object System.Data.SqlClient.SqlConnection "server=$svr;database=master;Integrated Security=sspi"
	$cn.Open()
	$sql = $cn.CreateCommand()
	$sql.CommandText = " SELECT TOP (1) @@servername AS ServerName, DatabaseVersion AS LiteSpeedVersion FROM [LiteSpeedLocal].[dbo].[LitespeedVersion] "
	$rdr = $sql.ExecuteReader()
	$dt.Load($rdr)
	$cn.Close()
}
$dt| select * -ExcludeProperty RowError, RowState, HasErrors, Name, Table, ItemArray | ConvertTo-Html -head $style -body "<h1>SQL Server LiteSpeed Version Report</h1>" | Set-Content $outputfilefull


Read-Host -Prompt "Press Enter to continue"

```