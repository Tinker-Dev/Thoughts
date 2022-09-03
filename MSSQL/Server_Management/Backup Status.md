# Backup Status

## Version 1

```
USE [master]
GO

SELECT session_id AS SPID
         ,command
         ,a.text AS Query
         ,start_time
         ,percent_complete
         ,DATEADD(SECOND, estimated_completion_time / 1000, GETDATE()) AS estimated_completion_time
FROM          sys.dm_exec_requests r
          CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
WHERE  r.command IN ( 'BACKUP DATABASE', 'RESTORE DATABASE' );

```

  

## Version 2

```
DECLARE @DBName VARCHAR(64) = 'Warehouse' 
DECLARE @ErrorLog AS TABLE([LogDate] CHAR(24), [ProcessInfo] VARCHAR(64), [TEXT] VARCHAR(MAX))

INSERT INTO @ErrorLogEXEC master..sp_readerrorlog 0, 1, 'Recovery of database', @DBName

INSERT INTO @ErrorLogEXEC master..sp_readerrorlog 0, 1, 'Recovery completed', @DBName
SELECT TOP 1 @DBName AS [DBName],
            [LogDate],
            CASE
              WHEN SUBSTRING([TEXT],10,1) = 'c'      
              THEN '100%'      
              ELSE SUBSTRING([TEXT], CHARINDEX(') is ', [TEXT]) + 4,CHARINDEX(' complete (', [TEXT]) - CHARINDEX(') is ', [TEXT]) - 4) END AS PercentComplete,
            CASE
              WHEN SUBSTRING([TEXT],10,1) = 'c'      
              THEN 0      
              ELSE CAST(SUBSTRING([TEXT], CHARINDEX('approximately', [TEXT]) + 13,CHARINDEX(' seconds remain', [TEXT]) - CHARINDEX('approximately', [TEXT]) - 13) AS FLOAT)/60.0 END AS MinutesRemaining,
            CASE      
              WHEN SUBSTRING([TEXT],10,1) = 'c'      
              THEN 0      
              ELSE CAST(SUBSTRING([TEXT], CHARINDEX('approximately', [TEXT]) + 13,CHARINDEX(' seconds remain', [TEXT]) - CHARINDEX('approximately', [TEXT]) - 13) AS FLOAT)/60.0/60.0 END AS HoursRemaining,
            [TEXT]
FROM @ErrorLog 
ORDER BY CAST([LogDate] as datetime) DESC, [MinutesRemaining]

```