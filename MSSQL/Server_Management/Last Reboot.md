# Last Reboot

This script will look at a list of servers and then run through and get the timestamp of the last reboot of the machine.  

  

```
$isodate=Get-Date -format s
$isodate=$isodate -replace(":","")
$basepath="C:\ServerPatching\OS_Patching\output"
$outputfile="\LastReboot_" + $isodate + ".html"
$outputfilefull = $basepath + $outputfile

$style="" 
$dt = new-object "System.Data.DataTable"
foreach ($svr in get-content "C:\ServerPatching\OS_Patching\serverlist.txt")
{ $svr 
	$cn = new-object System.Data.SqlClient.SqlConnection "server=$svr;database=master;Integrated Security=sspi"
	$cn.Open()
	$sql = $cn.CreateCommand()
	$sql.CommandText = " SELECT @@SERVERNAME AS ServerName,sqlserver_start_time FROM sys.dm_os_sys_info "
	$rdr = $sql.ExecuteReader() 
	$dt.Load($rdr) 
	$cn.Close()
}
$dt| select * -ExcludeProperty RowError, RowState, HasErrors, Name, Table, ItemArray | ConvertTo-Html -head $style -body "<h1>SQL Server Last Reboot Report</h1>" | Set-Content $outputfilefull

Read-Host -Prompt "Press Enter to continue"

```