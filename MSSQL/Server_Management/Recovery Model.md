# Recovery Model

This will tell you the Recovery Model for all of the databases on the server.  

```
SELECT name
	  ,recovery_model_desc
FROM   sys.databases;

GO

```