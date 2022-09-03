# Check For Primary Replica for Running Job

This bit of code is for when you have a Agent Server running jobs from outside the cluster. Now if you have clustering setup you generally have server name aliasing setup as well. This would allow you to point your jobs at the alias and not to a server directly. However even with all this work the system does mess up from time to time. The below code will make sure that you are running the job in question against the primary or it will fail the job. This way you don't get a false sense of completion. You just need to add this as a first job step and then configure it.  

  

```
DECLARE @CMD VARCHAR(MAX)
DECLARE @JobName VARCHAR(255)

SELECT @JobName = 'PUT JOB NAME HERE'


IF sys.fn_hadr_is_primary_replica ('PUT CLUSTER NAME HERE') = 1
BEGIN
   PRINT 'Server is primary. Go to Next step'
END
ELSE
BEGIN
   PRINT 'Server is secondary. Stop job'
   SELECT @CMD = 'EXEC msdb.dbo.sp_stop_job @job_name=N'''+@JobName+'''';
   PRINT @CMD
   EXEC (@CMD)
END

```