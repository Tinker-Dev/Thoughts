# Max Worker Threads

  

## Important Note

The default value for max worker threads is 0. This enables SQL Server to automatically configure the number of worker threads at startup. The default setting is best for most systems. However, depending on your system configuration, setting max worker threads to a specific value sometimes improves performance.  

  

## Limitations and Restrictions

- When the actual number of query requests is less than the amount set in max worker threads, one thread handles each query request. However, if the actual number of query request exceeds the amount set in max worker threads, SQL Server pools the worker threads so that the next available worker thread can handle the request.

  

## Recommendations

- This option is an advanced option and should be changed only by an experienced database administrator or certified SQL Server professional. If you suspect that there is a performance problem, it is probably not the availability of worker threads. The cause is more likely something like I/O that is causing the worker threads to wait. It is best to find the root cause of a performance issue before you change the max worker threads setting.
    
- Thread pooling helps optimize performance when large numbers of clients are connected to the server. Usually, a separate operating system thread is created for each query request. However, with hundreds of connections to the server, using one thread per query request can consume large amounts of system resources. The max worker threads option enables SQL Server to create a pool of worker threads to service a larger number of query requests, which improves performance.
    

  

## Suggested Values

- The following table shows the automatically configured number of max worker threads for various combinations of CPUs and versions of SQL Server.

  

| Number of CPUs | 32-bit computer | 64-bit computer |
| --- | --- | --- |
| 4 processors | 256 | 512 |
| 8 processors | 288 | 576 |
| 16 processors | 352 | 704 |
| 32 processors | 480 | 960 |
| 64 processors | 736 | 1472 |
| 128 processors | 4224 | 4480 |
| 256 processors | 8320 | 8576 |

  

### Formula:

The values above are created using the following formula:  

| Number of CPUs | 32-bit computer | 64-bit computer |
| --- | --- | --- |
| <=4 processors | 256 | 512 |
| \>4 processors and < 64 processors | 256 + ((logical CPU's - 4) \* 8) | 512 + ((logical CPU's - 4) \* 16) |
| \> 64 processors | 256 + ((logical CPU's - 4) \* 32) | 512 + ((logical CPU's - 4) \* 32) |

  

- When all worker threads are active with long running queries, SQL Server might appear unresponsive until a worker thread completes and becomes available. Although this is not a defect, it can sometimes be undesirable. If a process appears to be unresponsive and no new queries can be processed, then connect to SQL Server using the dedicated administrator connection (DAC), and kill the process. To prevent this, increase the number of max worker threads.
    
- The max worker threads server configuration option does not limit all threads that may be spanwed in the system. Threads required for tasks such as Availibility Groups, Service Broker, Lock Manager, or others are spawned outside this limit. If the number of threads configured is being exceeded, the following query will provide information about the system tasks that have spawned the additional threads. 
    

  

```
SELECT  s.session_id, r.command, r.status,  
   r.wait_type, r.scheduler_id, w.worker_address,  
   w.is_preemptive, w.state, t.task_state,  
   t.session_id, t.exec_context_id, t.request_id  
FROM sys.dm_exec_sessions AS s  
INNER JOIN sys.dm_exec_requests AS r  
   ON s.session_id = r.session_id  
INNER JOIN sys.dm_os_tasks AS t  
   ON r.task_address = t.task_address  
INNER JOIN sys.dm_os_workers AS w  
   ON t.worker_address = w.worker_address  
WHERE s.is_user_process = 0;

```

  

  

## Making the Change

### Permissions

Here are the permissions needed to make this change. Execute permissions on sp\_configure with no parameters or with only the first parameter are granted to all users by default. To execute sp\_configure with both parameters to change a configuration option or to run the RECONFIGURE statement, a user must be granted the ALTER SETTINGS server-level permission. The ALTER SETTINGS permission is implicitly held by the sysadmin and serveradmin fixed server roles.  

  

### Using SQL Server Management Studio

To configure the max worker threads option:

1. In Object Explorer, right-click a server and select Properties.
2. Click the Processors node.
3. In the Max worker threads box, type or select a value from 128 through 32,767.

  

### Using Transact-SQL

To configure the max worker threads option:

1. Connect to the Database Engine.
2. From the Standard bar, click New Query.
3. Copy and paste the following example into the query window and click Execute.

- This example shows how to use sp\_configure to configure the max worker threads option to 900.

```
USE AdventureWorks2012 ;  
GO  
EXEC sp_configure 'show advanced options', 1;  
GO  
RECONFIGURE ;  
GO  
EXEC sp_configure 'max worker threads', 900 ;  
GO  
RECONFIGURE;  
GO

```

  

    - The change will take effect immediately after executing RECONFIGURE, without requiring the Database Engine to restart.

  

```
RECONFIGURE [ WITH OVERRIDE ]

```