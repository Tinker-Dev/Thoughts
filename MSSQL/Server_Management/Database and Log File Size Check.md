# Database and Log File Size Check

```
DECLARE	@DBInfo table (	ServerName varchar(100)
						,DatabaseName varchar(100)
						,FileSizeMB int
						,LogicalFileName sysname
						,PhysicalFileName nvarchar(520)
						,Status sysname
						,Updateability sysname
						,RecoveryMode sysname
						,FreeSpaceMB int
						,FreeSpacePct varchar(7)
						,FreeSpacePages int
						,PollDate datetime);  

DECLARE	@command varchar(5000);  

SELECT	@command = 'Use [' + '?' + '] SELECT  
@@servername as ServerName,  
' + '''' + '?' + '''' + ' AS DatabaseName,  
CAST(sysfiles.size/128.0 AS int) AS FileSize,  
sysfiles.name AS LogicalFileName, sysfiles.filename AS PhysicalFileName,  
CONVERT(sysname,DatabasePropertyEx(''?'',''Status'')) AS Status,  
CONVERT(sysname,DatabasePropertyEx(''?'',''Updateability'')) AS Updateability,  
CONVERT(sysname,DatabasePropertyEx(''?'',''Recovery'')) AS RecoveryMode,  
CAST(sysfiles.size/128.0 - CAST(FILEPROPERTY(sysfiles.name, ' + '''' + 'SpaceUsed' + '''' + ' ) AS int)/128.0 AS int) AS FreeSpaceMB,  
CAST(100 * (CAST (((sysfiles.size/128.0 -CAST(FILEPROPERTY(sysfiles.name,  
' + '''' + 'SpaceUsed' + '''' + ' ) AS int)/128.0)/(sysfiles.size/128.0))  
AS decimal(4,2))) AS varchar(8)) + ' + '''' + '%' + '''' + ' AS FreeSpacePct,  
GETDATE() as PollDate FROM dbo.sysfiles';  
INSERT	INTO @DBInfo
		(
			ServerName
			,DatabaseName
			,FileSizeMB
			,LogicalFileName
			,PhysicalFileName
			,Status
			,Updateability
			,RecoveryMode
			,FreeSpaceMB
			,FreeSpacePct
			,PollDate
		)
		EXEC sp_MSforeachdb @command;  

SELECT	ServerName
		,DatabaseName
		,FileSizeMB
		,LogicalFileName
		,PhysicalFileName
		,Status
		,Updateability
		,RecoveryMode
		,FreeSpaceMB
		,FreeSpacePct
		,PollDate
FROM	@DBInfo
ORDER BY ServerName, DatabaseName;

```