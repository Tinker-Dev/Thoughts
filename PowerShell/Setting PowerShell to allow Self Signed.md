# Setting PowerShell to allow Self Signed Scripts

This is to allow Windows to run PowerShell scripts that you have created.  

  

```
powershell Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass

powershell Set-ExecutionPolicy RemoteSigned

Pause

```