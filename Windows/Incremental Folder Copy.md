# Incremental Folder Copy

If you would like the ability to backup you Documents folder to a thumb drive, then this script is a good place to start.  

  

```
Echo off

cls

echo.
echo.
echo Sync is ready to run!
echo.
echo You need to Save and Close everything.
echo.
echo.

Pause

if exist D:\ xcopy "C:\Users\UserName\Documents" "D:\Documents Backup" /E /C /H /R /Y /D /F

```