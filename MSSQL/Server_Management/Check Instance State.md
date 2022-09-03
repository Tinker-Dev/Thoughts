# Check Instance State

Looks a list of servers and then reports the state of the instances.  

```
$isodate=Get-Date -format s
$isodate=$isodate -replace(":","")
$basepath="C:\AfterPatchingPS\output"
$outputfile="\database_status_" + $isodate + ".html"
$outputfilefull = $basepath + $outputfile

$style="" 
$dt = new-object "System.Data.DataTable"
foreach ($svr in get-content "C:\AfterPatchingPS\serverlist.txt")
{ $svr 
        $cn = new-object System.Data.SqlClient.SqlConnection "server=$svr;database=master;Integrated Security=sspi"
        $cn.Open()
        $sql = $cn.CreateCommand()
        $sql.CommandText = " SELECT @@servername AS ServerName, name AS DatabaseName, state_desc AS DatabaseStatus FROM sys.databases "
        $rdr = $sql.ExecuteReader() 
        $dt.Load($rdr) 
        $cn.Close()
}
$dt| select * -ExcludeProperty RowError, RowState, HasErrors, Name, Table, ItemArray | ConvertTo-Html -head $style -body "<h1>SQL Server Database Status Report</h1>" | Set-Content $outputfilefull


Read-Host -Prompt "Press Enter to continue"

```