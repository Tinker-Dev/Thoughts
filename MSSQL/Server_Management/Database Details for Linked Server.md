# Database Details for Linked Server

```
USE DBAPlayground
GO

--/*

DROP TABLE dbo.DatabaseDetails
GO

CREATE TABLE dbo.DatabaseDetails (
								ServerName nvarchar(256)
								,DBName nvarchar(256)
								,Drive nvarchar(512)
								,FreeSpaceOnDriveInMB int
								,PhysicalFileLocation nvarchar(520)
								,CaptureDate datetime
								)

--*/



INSERT INTO dbo.DatabaseDetails
		(
			ServerName
			,DBName
			,Drive
			,FreeSpaceOnDriveInMB
			,PhysicalFileLocation
			,CaptureDate
		)


SELECT	*
FROM	OPENQUERY(YOURSERVER, 'SELECT DISTINCT 
									   @@SERVERNAME as ServerName
									   ,DB_NAME(ov.database_id) DBName 
									   ,ov.volume_mount_point AS Drive 
									   ,CONVERT(INT, ov.available_bytes / 1048576.0) AS FreeSpaceOnDriveInMB
									   ,mf.physical_name PhysicalFileLocation
									   ,GetDate() AS CaptureDate
								FROM sys.master_files mf
									 CROSS APPLY sys.dm_os_volume_stats(mf.database_id, mf.FILE_ID) ov
								WHERE ov.database_id > 4
								ORDER BY FreeSpaceOnDriveInMB ASC;
								');
GO


--SELECT ServerName, COUNT(DISTINCT DBName) DBCount
--FROM dbo.DatabaseDetails
--GROUP BY ServerName
--GO

SELECT TOP 10 * 
FROM dbo.DatabaseDetails
ORDER BY ServerName,DBName

```