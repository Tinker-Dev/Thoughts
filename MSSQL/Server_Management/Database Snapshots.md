# Database Snapshots

## Create

### Option 1 - Build a Create Snapshot Script for Every Non-System DB

This SQL script will create a database snapshot run script for ALL databases except system databases. Then you can copy/paste/run the scripts for the databases of your choosing.  

```
SELECT 'CREATE DATABASE [SS_' + DB_NAME(database_id) + '] ON (Name = [' + name + '], FILENAME =''' + 'D:\MSSQL\DATA\'
	   + name + 'SS_.SS'')' + ' as SNAPSHOT OF [' + DB_NAME(database_id) + ']'
FROM   sys.master_files
WHERE  type = 0
	   AND DB_NAME(database_id) NOT IN ( 'master', 'model', 'msdb', 'tempdb', 'reportserver', 'reportservertempdb' )
	   AND database_id NOT IN (
								SELECT database_id
								FROM   sys.master_files
								WHERE  file_id > 2
							  );

```

  

### Option 2 - Create a Focused Script For One DB

This will build the create script for just the one DB that you choose.  

```
SELECT 0 ordercol
	  ,database_id
	  ,'CREATE DATABASE [SS_' + DB_NAME(database_id) + '] ON ' SQLQuery
FROM   sys.master_files
WHERE  DB_NAME(database_id) IN ( 'RAHUL' )
	   AND type = 0
	   AND file_id = 1
UNION
SELECT file_id ordercol
	  ,database_id
	  ,'  (Name = [' + name + '], FILENAME =''' + 'D:\MSSQL\DATA\' + name + 'SS_.SS''),'
FROM   sys.master_files
WHERE  DB_NAME(database_id) IN ( 'RAHUL' )
	   AND type = 0
UNION
SELECT 9999 AS ordercol
	  ,database_id
	  ,' as SNAPSHOT OF ' + DB_NAME(database_id)
FROM   sys.master_files
WHERE  DB_NAME(database_id) IN ( 'RAHUL' )
	   AND type = 0
	   AND file_id = 1
	   AND type = 0
	   AND data_space_id = 1
ORDER BY database_id
		,ordercol;

```

  

## Remove all Database Snapshots

### Build Drop Snapshot Scripts for all Non-System DBs

```
SELECT DISTINCT 'DROP DATABASE SS_' + DB_NAME(database_id)
FROM   sys.master_files
WHERE  DB_NAME(database_id) NOT IN ( 'master', 'model', 'msdb', 'tempdb', 'reportserver', 'reportservertempdb' )
	   AND type = 0
	   AND data_space_id = 1;

```

  

## Restore From a Database Snapshot

**This will restore everything. You may want to limit what you restore.**  

**

```
SELECT 'RESTORE DATABASE [' + name + '] FROM DATABASE_SNAPSHOT = ''' + 'SS_' + name + ''''
FROM   sysdatabases
WHERE  name NOT IN ( 'master', 'model', 'msdb', 'tempdb', 'reportserver', 'reportservertempdb' );

```

  


**