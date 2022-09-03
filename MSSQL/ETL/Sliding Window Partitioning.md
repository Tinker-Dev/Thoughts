# Sliding Window Partitioning

**_NOTE THIS IS NO COMPLETE_**  

  

Taken From: [https://www.mssqltips.com/sqlservertip/5296/implementation-of-sliding-window-partitioning-in-sql-server-to-purge-data/](https://www.mssqltips.com/sqlservertip/5296/implementation-of-sliding-window-partitioning-in-sql-server-to-purge-data/)  

  

## SQL Server Partition Switching

Partition switching will allow you to move a partition between source and target tables very quickly. As this is a metadata-only operation, no data movement will happen during the switching. Hence this is extremely fast. Using this methodology, the old data can be switched to a work (Staging) table and then the data in the work table can be archived and purged. In sliding window methodology, we don’t create new files/file group when we create new partition. We purge/archive an old partition and we reuse that partition to receive new data. Hence in a repeated/circular method, we try to use the same data files/filegroups again and again.  

  

## Partition Switching Points to Consider

The following criteria must be met for partition switching: 1 The target table must be empty 2 Source and target tables must have identical columns and indexes 3 Both the source and target table must use the same column as the partition column 4 Both the source and target tables must be in the same filegroup If you try to switch the partition without satisfying any of the criteria, the SQL Server will throw an exception and will provide a detailed error message.  

  

## SQL Server Partition Switching Syntax

Partition switching can be accomplished by using the "ALTER TABLE SWITCH" statement.  

  

## Sliding Window Partition Steps in SQL Server

There are 5 steps to implement in the Sliding Window Partition: 

1. Switching partition between main and work table 
2. Purge or archive data from the work table 
3. Prepare the filegroup to accept new boundaries 
4. Split the right most partition based on a new boundary
5. Merge the old partition with the new boundary

  

## Example Solution

In this example, I have created an Orders table with order date as the partition column. This table will be created with 3 partitions to accept data on a monthly basis. Initially partition one will accept data until November 30, 2017 and the second partition will accept data between Dec 1, 2017 and Dec 31, 2017. The last empty partition will accept data from Jan 1, 2018 onwards. The below script will create physical data files, file groups, partition scheme, partition function and the Order table. Also, this script will load sample data for a few months.  

  

###   

### Cleaning up the Demo Area

```
USE [master]
GO

--Drop if the DB already exists 
If exists(Select name from sys.databases where name = 'Staging_TST')
Begin
   Drop database Staging_TST
End
Go

--Create the DB
CREATE DATABASE [Staging_TST]
CONTAINMENT = NONE
ON PRIMARY ( NAME = N'Staging_TST', FILENAME = N'C:\Program Files\Microsoft SQL Server\MSSQL11.SQL2012\MSSQL\DATA\Staging_TST.mdf' , SIZE = 28672KB , MAXSIZE = UNLIMITED, FILEGROWTH = 1024KB ) 
LOG ON ( NAME = N'Staging_TST_log', FILENAME = N'C:\Program Files\Microsoft SQL Server\MSSQL11.SQL2012\MSSQL\DATA\Staging_TST_log.ldf' , SIZE = 470144KB , MAXSIZE = 2048GB , FILEGROWTH = 10%)
go

--Remove physical database files for 3 partitioning
If (Exists(Select name from sys.database_files where name='Staging_TST_01'))
Begin
   Alter Database Staging_TST
   Remove file Staging_TST_01
End
Go

If (Exists(Select name from sys.database_files where name='Staging_TST_02'))
Begin
   Alter Database Staging_TST
   Remove file Staging_TST_02
End
Go

If (Exists(Select name from sys.database_files where name='Staging_TST_03'))
Begin
   Alter Database Staging_TST
   Remove file Staging_TST_03
End
Go

----Remove file groups for 3 partitioning
If (Exists(Select name from sys.filegroups where name='Staging_TSTFG_01'))
Begin
   Alter Database Staging_TST
   Remove Filegroup Staging_TSTFG_01
End
Go

If (Exists(Select name from sys.filegroups where name='Staging_TSTFG_02'))
Begin
   Alter Database Staging_TST
   Remove Filegroup Staging_TSTFG_02
End
Go

If (Exists(Select name from sys.filegroups where name='Staging_TSTFG_03'))
Begin
   Alter Database Staging_TST
   Remove Filegroup Staging_TSTFG_03
End
Go

```

  

###   

### Create the Structure

```
Use Master
go

--Create FileGroups for partitioning
ALTER DATABASE Staging_TST
ADD FILEGROUP Staging_TSTFG_01 
GO

ALTER DATABASE Staging_TST
ADD FILE 
(
NAME = [Staging_TST_01], 
FILENAME = --'K:\Program Files\Microsoft SQL Server\MSSQL11.SIR\MSSQL\Data\Staging\Staging_TST_01.ndf', 
'C:\Program Files\Microsoft SQL Server\MSSQL11.SQL2012\MSSQL\DATA\Staging\Staging_TST_01.ndf', 
SIZE = 5242880 KB, 
MAXSIZE = UNLIMITED, 
FILEGROWTH = 5242880 KB
) TO FILEGROUP Staging_TSTFG_01
GO

ALTER DATABASE Staging_TST
ADD FILEGROUP Staging_TSTFG_02 
GO

ALTER DATABASE Staging_TST
ADD FILE 
(
NAME = [Staging_TST_02], 
FILENAME = 
'C:\Program Files\Microsoft SQL Server\MSSQL11.SQL2012\MSSQL\DATA\Staging\Staging_TST_02.ndf', 
SIZE = 5242880 KB, 
MAXSIZE = UNLIMITED, 
FILEGROWTH = 5242880 KB
) TO FILEGROUP Staging_TSTFG_02
GO

ALTER DATABASE Staging_TST
ADD FILEGROUP Staging_TSTFG_03 
GO

ALTER DATABASE Staging_TST
ADD FILE 
(
NAME = [Staging_TST_03], 
FILENAME = 
'C:\Program Files\Microsoft SQL Server\MSSQL11.SQL2012\MSSQL\DATA\Staging\Staging_TST_03.ndf', 
SIZE = 5242880 KB, 
MAXSIZE = UNLIMITED, 
FILEGROWTH = 5242880 KB
) TO FILEGROUP Staging_TSTFG_03
GO

Use Staging_TST
go

CREATE PARTITION FUNCTION OrderPartitionFunction (Datetime) 
AS RANGE LEFT FOR VALUES ('20171201', '20180101'); 
GO

CREATE PARTITION SCHEME OrderPartitionScheme
AS PARTITION OrderPartitionFunction
TO (Staging_TSTFG_01,Staging_TSTFG_02,Staging_TSTFG_03);
GO

--Creating Orders table
CREATE TABLE Orders (
OrderID INT IDENTITY NOT NULL,
OrderDate DATETIME NOT NULL,
CustomerID INT NOT NULL, 
OrderStatus CHAR(1) NOT NULL DEFAULT 'P',
ShippingDate DATETIME
);
Go

ALTER TABLE Orders ADD CONSTRAINT PK_Orders PRIMARY KEY Clustered (OrderID, OrderDate)
ON OrderPartitionScheme (OrderDate);
Go

```

  

###   

### Add Some Data

```
INSERT INTO [dbo].[Orders]([OrderDate],[CustomerID],[OrderStatus],[ShippingDate])
VALUES(DateAdd(d, ROUND(DateDiff(d, '2017-10-01', '2017-12-31') * RAND(CHECKSUM(NEWID())), 0),DATEADD(second,CHECKSUM(NEWID())%48000, '2017-10-01')),ABS(CHECKSUM(NewId())) % 1000,'P',DateAdd(d, ROUND(DateDiff(d, '2017-10-01', '2017-12-31') * RAND(CHECKSUM(NEWID())), 0),DATEADD(second,CHECKSUM(NEWID())%48000, '2017-10-01')))
GO 1000 -- And lets do that a few times.

```

  

###   

### View Partition Details

- This is going to be used a lot in order to look at what is going on with the partitioning so lets just create it as a proc.

  

```
Create procedure dbo.PartitionDetails
as
SELECT
OBJECT_SCHEMA_NAME(pstats.object_id) AS SchemaName
,OBJECT_NAME(pstats.object_id) AS TableName
,ps.name AS PartitionSchemeName
,ds.name AS PartitionFilegroupName
,pf.name AS PartitionFunctionName
,CASE pf.boundary_value_on_right WHEN 0 THEN 'Range Left' ELSE 'Range Right' END AS PartitionFunctionRange
,CASE pf.boundary_value_on_right WHEN 0 THEN 'Upper Boundary' ELSE 'Lower Boundary' END AS PartitionBoundary
,prv.value AS PartitionBoundaryValue
,c.name AS PartitionKey
,CASE 
WHEN pf.boundary_value_on_right = 0 
THEN c.name + ' > ' + CAST(ISNULL(LAG(prv.value) OVER(PARTITION BY pstats.object_id ORDER BY pstats.object_id, pstats.partition_number), 'Infinity') AS VARCHAR(100)) + ' and ' + c.name + ' <= ' + CAST(ISNULL(prv.value, 'Infinity') AS VARCHAR(100)) 
ELSE c.name + ' >= ' + CAST(ISNULL(prv.value, 'Infinity') AS VARCHAR(100)) + ' and ' + c.name + ' < ' + CAST(ISNULL(LEAD(prv.value) OVER(PARTITION BY pstats.object_id ORDER BY pstats.object_id, pstats.partition_number), 'Infinity') AS VARCHAR(100))
END AS PartitionRange
,pstats.partition_number AS PartitionNumber
,pstats.row_count AS PartitionRowCount
,p.data_compression_desc AS DataCompression
FROM sys.dm_db_partition_stats AS pstats
INNER JOIN sys.partitions AS p ON pstats.partition_id = p.partition_id
INNER JOIN sys.destination_data_spaces AS dds ON pstats.partition_number = dds.destination_id
INNER JOIN sys.data_spaces AS ds ON dds.data_space_id = ds.data_space_id
INNER JOIN sys.partition_schemes AS ps ON dds.partition_scheme_id = ps.data_space_id
INNER JOIN sys.partition_functions AS pf ON ps.function_id = pf.function_id
INNER JOIN sys.indexes AS i ON pstats.object_id = i.object_id AND pstats.index_id = i.index_id AND dds.partition_scheme_id = i.data_space_id AND i.type <= 1 /* Heap or Clustered Index */
INNER JOIN sys.index_columns AS ic ON i.index_id = ic.index_id AND i.object_id = ic.object_id AND ic.partition_ordinal > 0
INNER JOIN sys.columns AS c ON pstats.object_id = c.object_id AND ic.column_id = c.column_id
LEFT JOIN sys.partition_range_values AS prv ON pf.function_id = prv.function_id AND pstats.partition_number = (CASE pf.boundary_value_on_right WHEN 0 THEN prv.boundary_id ELSE (prv.boundary_id+1) END)
ORDER BY TableName, PartitionNumber;
Go
Execute dbo.PartitionDetails
Go

```

  

###   

### Run the Proc

```
Execute dbo.PartitionDetails
Go

```

  

  

## Partition Switching

```
--Creating Orders Staging tableCREATE TABLE Orders_Work (
OrderID INT IDENTITY NOT NULL,
OrderDate DATETIME NOT NULL,
CustomerID INT NOT NULL, 
OrderStatus CHAR(1) NOT NULL DEFAULT 'P',
ShippingDate DATETIME
);
Go

ALTER TABLE Orders_Work ADD CONSTRAINT PK_Orders_Work PRIMARY KEY Clustered (OrderID, OrderDate)
ON OrderPartitionScheme (OrderDate);
Go

Execute dbo.PartitionDetails
Go

```

  

  

### Purge data from the Working table

```
Truncate table Orders_Work
Go

```

  

  

### Partition Splitting

```
Alter Partition Scheme OrderPartitionScheme NEXT USED Staging_TSTFG_01
Go

```

  

  

### Partition Merge

```
Alter Partition Function OrderPartitionFunction() SPLIT RANGE ('20180201');
Go

Alter Partition Function OrderPartitionFunction() MERGE RANGE ('20171201');
Go

```

  

  

### Automate Partition Creation

```
If Exists(Select Name from sys.procedures where name='CreateNextPartition')
Drop procedure CreateNextPartition
Create procedure dbo.CreateNextPartition (@DtNextBoundary as datetime)
as
Begin
Declare @DtOldestBoundary AS datetime
Declare @strFileGroupToBeUsed AS VARCHAR(100)
Declare @PartitionNumber As int
SELECT @strFileGroupToBeUsed = fg.name, @PartitionNumber = p.partition_number, @DtOldestBoundary = cast(prv.value as datetime) FROM sys.partitions p 
INNER JOIN sys.sysobjects tab on tab.id = p.object_id
INNER JOIN sys.allocation_units au ON au.container_id = p.hobt_id 
INNER JOIN sys.filegroups fg ON fg.data_space_id = au.data_space_id 
INNER JOIN SYS.partition_range_values prv ON prv.boundary_id = p.partition_number
INNER JOIN sys.partition_functions PF ON pf.function_id = prv.function_id
WHERE 1=1
AND pf.name = 'OrderPartitionFunction'
AND tab.name = 'Orders'
AND cast(value as datetime) = (
SELECT MIN(cast(value as datetime)) FROM sys.partitions p 
INNER JOIN sys.sysobjects tab on tab.id = p.object_id
INNER JOIN SYS.partition_range_values prv ON prv.boundary_id = p.partition_number
INNER JOIN sys.partition_functions PF ON pf.function_id = prv.function_id
WHERE 1=1
AND pf.name = 'OrderPartitionFunction'
AND tab.name = 'Orders'
)
Select @DtOldestBoundary Oldest_Boundary , @strFileGroupToBeUsed FileGroupToBeUsed,@PartitionNumber PartitionNumber
ALTER TABLE Orders SWITCH PARTITION @PartitionNumber TO Orders_Work PARTITION @PartitionNumber
TRUNCATE TABLE Orders_Work
EXEC('Alter Partition Scheme OrderPartitionScheme NEXT USED '+@strFileGroupToBeUsed)
Alter Partition Function OrderPartitionFunction() SPLIT RANGE (@DtNextBoundary);
Alter Partition Function OrderPartitionFunction() MERGE RANGE (@DtOldestBoundary);
End
Go

```

  

  

### Kick off the Procedure:

```
Execute CreateNextPartition '20180301'
Go

```

  

  

## Partition Truncation in SQL Server 2016

- SQL Server 2016 supports the truncation of a partition, so we can directly purge the partition data. Hence, we don’t need any work/staging table and we can skip the partition switch method.
- The partition truncation can be achieved by using the command:

```
Truncate table <TableName> (with Partitions (PartitionNumber))

```

  

In our example the first partition data can be truncated using the below command.  

```
Truncate Table Orders with (Partitions(1))
Go

```