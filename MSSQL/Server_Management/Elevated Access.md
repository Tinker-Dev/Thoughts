# Elevated Access

- I personally have been in a situation were someone messed up the permission on a server and the server admin lost access to a folder. This is a means to get access to the content that might have otherwise been lost.
    
- This script does require that you to grab "psexec" from here: [https://live.sysinternals.com/](https://live.sysinternals.com/)
    
- You will also need elevated rights on the server. They don't have to be the "admin" account.
    

  

```
C:\SysinternalsSuite\psexec -i -s cmd.exe

```

  

## ⁠Things to do with Elevated Access

**Reset the Permissions on a folder structure**  

**

```
icacls "D:\MSSQL13.MSSQLSERVER" /reset /T

```

  


****Give the USERS group access**

**

```
icacls "MSSQL13.MSSQLSERVER" /grant USERS:F /t

```

  


****Grant the Administrator group access to something**

**

```
icacls "MSSQL13.MSSQLSERVER" /grant "Administrator":F /t

```

  

or**

**

```
takeown /F MSSQL13.MSSQLSERVER /A

```

  

  
**