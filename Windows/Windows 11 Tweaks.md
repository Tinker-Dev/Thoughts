# Windows 11 Tweaks

  

## 

* * *

## Full Right-Click Menu in Explorer

Run this at a non-admin command prompt:

```
reg add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve

```

  

Then restart `Explorer.exe` or Reboot.

  

## 

* * *

## Default Right-Click Menu in Explorer

Run this at a non-admin command prompt:  

```
reg delete "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}" /f

```

  

Then restart `Explorer.exe` or Reboot.  

  

* * *