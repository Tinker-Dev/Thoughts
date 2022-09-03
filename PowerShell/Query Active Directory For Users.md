# Query Active Directory For Users

```
# Import Active Directory Module 
try 
{ 
    import-module activedirectory  
} 
catch 
{ 
    write-Output "Error while importing Active Directly module, please install AD Powershell modules first before running this script." 
} 
 
# Get AD Users and export results to CSV 

Get-ADUser -Filter 'Name -like "*"' -Properties * | Select SamAccountName, DisplayName, CN, Title, Department, Manager, Company, EmailAddress, OfficePhone, Enabled, whenCreated, whenChanged | Export-Csv c:\ADUsers.csv -NoTypeInformation

Read-Host -Prompt "Press Enter to continue"

```