# Incremental Database Shrink

```
param
(
    [Parameter(Mandatory=$True)][string]$SQLInstanceName,
    [Parameter(Mandatory=$True)][string]$DatabaseName,
    [Parameter(Mandatory=$True)][string]$FileGroupName,
    [Parameter(Mandatory=$True)][string]$FileName,
    [Parameter(Mandatory=$True)][int]$ShrinkSizeMB,
    [Parameter(Mandatory=$True)][int]$ShrinkIncrementMB = 10
)
[system.Reflection.Assembly]::LoadWithPartialName("Microsoft.SQLServer.Smo")
$server = New-Object Microsoft.SqlServer.Management.Smo.Server $SQLInstanceName
$Database = $Server.Databases[$DatabaseName]
$Filegroup = $Database.filegroups[$FileGroupName]
$File = $Filegroup.Files[$FileName]
$SizeMB = $File.Size/1024
"Starting Size = $SizeMB"
if($SizeMB - $file.AvailableSpace -le $ShrinkSizeMB)
{
    while ($SizeMB -ge $ShrinkSizeMB)
    {
        $File.Shrink($SizeMB - $ShrinkIncrementMB, [Microsoft.SQLServer.Management.SMO.ShrinkMethod]::Default)
        $Server.Refresh()
        $Database.Refresh()
        $File.Refresh()
        $SizeMB = $File.Size/1024
        "Size after shrink = $SizeMB"
    }
}

```