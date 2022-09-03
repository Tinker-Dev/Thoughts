# Delete Files Not Used in X Days

- Produces a CSV of files that have not been written to in `x Number of Days`
- To Change the number of days, modify the following portion in the first line: `AddDays(-124)`
- To Change the directory to search, modify this section in the first line: `\\yourservername\Backups`
- To Change the output file, modify the second line and place the full path to the desired file in the quotes: `export-Csv "C:\Temp\Old Clive Backups.csv"`
- The delete command that is generated should be run from within SQL Server on the server where the backup took place. It utilizes xp\_cmdshell, which may need to be enabled. Details on enabling can be found here: [https://msdn.microsoft.com/en-us/library/ms190693.aspx](https://msdn.microsoft.com/en-us/library/ms190693.aspx)

  

```

$oldfiles = Get-ChildItem \\yourservername\Backups -Recurse | Where{$_.LastWriteTime -lt (Get-Date).AddDays(-124)} | Where{$_.gettype().Name -eq "FileInfo"} | Select-Object DirectoryName, FullName, LastWriteTime, @{Name=”MegaBytes”;Expression={$_.Length / 1MB}}, @{Name="DeleteCommand";Expression={"EXEC xp_cmdshell 'del "+ $_.FullName + "'"}}

$oldfiles | export-Csv "C:\Temp\FileNameHere.csv" -NoTypeInformation -Delimiter ","

```