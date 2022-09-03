# Bulk Update the Job Owner

```
USE [master]
GO

DECLARE @PerviouseJobOwner nvarchar(128) = NULL
DECLARE @NewJobOwner nvarchar(128) = NULL

-- Make Changes Here

SET @PerviouseJobOwner = N'USERNAME_HERE'
SET @NewJobOwner = N'sa'

-- Stop Changes

SELECT [name] AS [JobName]
       ,COALESCE(SUSER_SNAME([Jobs].[owner_sid]), '') AS [CurrentOwner]
       ,'EXEC msdb.dbo.sp_update_job @job_name=N''' + [name] + ''', @owner_login_name=N''' + @NewJobOwner + ''';' AS [ChangeToNewOwner]
       ,'EXEC msdb.dbo.sp_update_job @job_name=N''' + [name] + ''', @owner_login_name=N''' + COALESCE(SUSER_SNAME([Jobs].[owner_sid]), '') + ''';' AS [RevertToOriginalOwner]
FROM [msdb].[dbo].[sysjobs] AS [Jobs]
WHERE [Jobs].[owner_sid] = SUSER_SID(@PerviouseJobOwner)

```