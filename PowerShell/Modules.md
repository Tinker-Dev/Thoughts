# Modules

## How to Save a Module

From time to time you will want to look at a module before you install it. To do this you can choose to save it first. Below is the code to do that.  

```
Save-Module -Name ModuleNameHere -Path c:/saved_modules/

```

  

## Modules with Installing

### Active Directory

```
Import-Module ServerManager
Add-WindowsFeature RSAT-AD-PowerShell

```