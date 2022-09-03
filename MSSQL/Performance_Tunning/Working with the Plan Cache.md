# Working with the Plan Cache

  

## Looking at the Plan Cache

```
-- View items in the Plan Cache
SELECT plan_handle
	  ,st.text
FROM   sys.dm_exec_cached_plans ps
	   CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS st
WHERE  st.text LIKE N'--- Special Plan%';

```

  

## Remove an Item from the Plan Cache

```
/* 
DBCC FREEPROCCACHE (0x060006006605380C40C1DAA7120000000000000000000000);  
*/

```