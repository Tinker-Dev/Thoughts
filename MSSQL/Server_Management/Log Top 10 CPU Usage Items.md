# Log Top 10 CPU Usage Items

```
#Enter the full path and name of the log file to store the results here
$LogFileName = "C:\PersonalPowershell\LOG.txt"

#enter the name of the server to check here
$server = "YOURSERVERNAMEHERE"

#Initialize Variables to NULL/Empty Array
$Processes = $null
$CpuCores = $null
$Samples = $null
$ProcessName = $null
$Process = $null
$CPUList = @()

#Get the number of cores on the monitored server. This is needed to calculate the Percent CPU.
$CpuCores = (Get-WMIObject -computer $server Win32_ComputerSystem).NumberOfLogicalProcessors
#Get a list of distinct process names currently running on the server
$Processes = Get-Process -ComputerName $server | Select ProcessName -Unique

#For each process found, use the WMI Performance Class to measure the CPU percentage, and add the results for each measurement to the CPUList array 
<#foreach($Process in $Processes){
    $ProcessName = $Process.ProcessName
    $Samples = (Get-Counter -ComputerName $server “\Process($Processname*)\% Processor Time”).CounterSamples
    $CPUList += ($Samples | Select InstanceName, @{Name=”PercentCPU”;Expression={[Decimal]::Round(($_.CookedValue / $CpuCores), 2)}})
    }
#>
$Samples = (Get-Counter -ComputerName $server -Counter "\Process(*)\% Processor Time").CounterSamples
$CPUList += ($Samples | Select InstanceName, @{Name=”PercentCPU”;Expression={[Decimal]::Round(($_.CookedValue / $CpuCores), 2)}})

#select the top 10 processes by CPU percentage and write the results to the log file
$server | Out-File -Append $LogFileName
get-date | Out-File -Append $LogFileName
$CPUList | sort PercentCPU -Descending | select -First 10 | format-table -auto | Out-File -Append $LogFileName

```