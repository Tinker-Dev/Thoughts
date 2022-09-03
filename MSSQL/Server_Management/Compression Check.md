# Compression Check

```
IF OBJECT_ID('tempdb..#CompCheck') IS NOT NULL DROP TABLE #CompCheck	

CREATE TABLE #CompCheck (TableName sysname NULL
							,IndexName sysname NULL
							,object_id int NULL
							,partition_id bigint NULL
							,partition_number int NULL
							,data_compression tinyint NULL
							,data_compression_desc nvarchar(120) NULL);


DECLARE	@command varchar(1000); 
SELECT	@command = 'IF ''?'' NOT IN(''master'', ''model'', ''msdb'', ''tempdb'') BEGIN
					USE [?]
					SELECT st.name, ix.name , st.object_id, sp.partition_id, sp.partition_number, sp.data_compression,sp.data_compression_desc
					FROM sys.partitions SP 
					INNER JOIN sys.tables ST ON st.object_id = sp.object_id 
					LEFT OUTER JOIN sys.indexes IX ON sp.object_id = ix.object_id and sp.index_id = ix.index_id
					order by st.name, sp.index_id
					END';


INSERT	#CompCheck
		(
			TableName
			,IndexName
			,object_id
			,partition_id
			,partition_number
			,data_compression
			,data_compression_desc
		)
		EXEC sp_MSforeachdb @command; 		



SELECT * 
FROM #CompCheck
WHERE data_compression <> 0

```