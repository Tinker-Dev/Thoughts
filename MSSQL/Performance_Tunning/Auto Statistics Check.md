# Auto Statistics Check

```
USE master;
GO
SELECT name AS 'Name',
       CASE
           WHEN is_auto_create_stats_on = 1 THEN
               'Yes'
           ELSE
               'No'
       END AS 'IsAutoCreateStatsOn',
       CASE
           WHEN is_auto_update_stats_on = 1 THEN
               'Yes'
           ELSE
               'No'
       END AS 'IsAutoUpdateStatsOn',
       CASE
           WHEN is_read_only = 1 THEN
               'Yes'
           ELSE
               'No'
       END AS 'IsDatabaseReadOnly'
FROM sys.databases
ORDER BY Name;

```