# Isolate Poor Performing Stored Procedures

```
IF OBJECT_ID('tempdb..#WorkList') IS NOT NULL
	DROP TABLE #WorkList;

SELECT TOP 100
			DB_NAME(database_id) AS DBName
			,OBJECT_SCHEMA_NAME(object_id, database_id) AS [Schema_Name]
			,OBJECT_NAME(object_id, database_id) AS [Object_Name]
			,total_elapsed_time / execution_count AS Avg_Elapsed_Time
			,execution_count AS Execution_Count
			,H.query_plan AS Query_Plan
			--,t.text AS Query_Text
INTO		#WorkList
FROM		sys.dm_exec_procedure_stats
--CROSS APPLY sys.dm_exec_sql_text(sql_handle) T
CROSS APPLY sys.dm_exec_query_plan(plan_handle) H
WHERE		DB_NAME(database_id) NOT IN ('master', 'tempdb', 'model', 'msdb', 'resource', 'SSISDB')
ORDER BY	Avg_Elapsed_Time DESC;


SELECT		*
FROM		#WorkList

```