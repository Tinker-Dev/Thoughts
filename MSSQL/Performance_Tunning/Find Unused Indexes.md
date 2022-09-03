# Find Unused Indexes

```
IF OBJECT_ID('tempdb..#UnusedIX') IS NOT NULL
		DROP TABLE #UnusedIX;


CREATE TABLE #UnusedIX (DatebaseName nvarchar(256)
						,ObjectName nvarchar(256)
						,IndexName nvarchar(256)
						,UserSeeks bigint
						,UserScans bigint
						,UserLookUps bigint
						,UserUpdates bigint
						,IndexSizeMB decimal(30,0))

DECLARE	@command varchar(1000); 

SELECT	@command = 'IF ''?'' NOT IN(''master'', ''model'', ''msdb'', ''tempdb'', ''SSISDB'') BEGIN USE [?]
SELECT  DB_NAME() AS DatebaseName   
		,OBJECT_NAME(S.object_id) AS ObjectName
        ,I.name AS IndexName
        ,SUM(user_seeks) AS UserSeeks
        ,SUM(user_scans) AS UserScans
        ,SUM(user_lookups) AS UserLookUps
        ,SUM(user_updates) AS UserUpdates
		,((SUM(ps.[used_page_count]) * 8) / 1024) AS IndexSizeMB
FROM            sys.dm_db_index_usage_stats AS S
            INNER JOIN sys.indexes AS I
                 ON I.object_id = S.object_id
                    AND I.index_id = S.index_id
			INNER JOIN sys.dm_db_partition_stats ps
				ON I.object_id = ps.object_id
                    AND I.index_id = ps.index_id 
WHERE    OBJECTPROPERTY(S.object_id, ''IsUserTable'') = 1
AND I.name IS NOT NULL
AND (S.user_seeks = 0 AND S.user_scans = 0 AND S.user_lookups = 0)
AND I.name NOT LIKE ''PK_%''
GROUP BY OBJECT_NAME(S.object_id),I.name
ORDER BY [ObjectName] END'; 

INSERT	INTO #UnusedIX
		(
			DatebaseName
			,ObjectName
			,IndexName
			,UserSeeks
			,UserScans
			,UserLookUps
			,UserUpdates
			,IndexSizeMB
		)
		EXEC sp_MSforeachdb @command; 


SELECT * 
FROM #UnusedIX
ORDER BY IndexSizeMB DESC



/*--
ALTER INDEX INDEXNAME ON TABLENAME DISABLE;
--*/

```