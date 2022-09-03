# MDF Only Restore

```
CREATE DATABASE AdventureWorks2012
  ON (
	   FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.VMSQLSERVER\MSSQL\DATA\AdventureWorksLT2012_Data.mdf'
	 )
  FOR ATTACH_REBUILD_LOG;

```