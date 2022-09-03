# Dynamic Import of an Excel file into MS SQL Server

Here is the code to import a partially formatted Excel file into a MS SQL Server Table.  

  

## Create Table “VariableData” Script

```
USE [TestDB]
GO

/****** Object:  Table [dbo].[VariableData]    Script Date: 8/13/2019 11:24:28 AM ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

CREATE TABLE [dbo].[VariableData](
	[VariableDataID] [bigint] IDENTITY(1,1) NOT NULL,
	[GroupNumber] [bigint] NOT NULL,
	[Batch] [int] NOT NULL,
	[AccountKey] [nvarchar](255) NULL,
	[ColumnName] [nvarchar](255) NULL,
	[ColumnDetail] [nvarchar](255) NULL,
PRIMARY KEY CLUSTERED 
(
	[VariableDataID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO

```

  

## Create Table “VariableDataType” Script

```
USE [TestDB]
GO

/****** Object:  Table [dbo].[VariableDataType]    Script Date: 8/13/2019 11:24:34 AM ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

CREATE TABLE [dbo].[VariableDataType](
	[VariableDataTypeID] [bigint] IDENTITY(1,1) NOT NULL,
	[GroupNumber] [bigint] NOT NULL,
	[Batch] [int] NOT NULL,
	[ColumnName] [nvarchar](255) NULL,
	[DataType] [nvarchar](255) NULL,
PRIMARY KEY CLUSTERED 
(
	[VariableDataTypeID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO

```

  

## View and Pivot Script

```
USE [TestDB]
GO

---------------------------------------------------- BEGIN TO BE DELETED ------------------------------------------------------

-- TRUNCATE TABLE [dbo].[VariableData]
-- SELECT * FROM [dbo].[VariableData]
-- TRUNCATE TABLE [dbo].[VariableDataType]
-- SELECT * FROM [dbo].[VariableDataType]

---------------------------------------------------- END TO BE DELETED ------------------------------------------------------


---------------------------------------------------- DEBUG ------------------------------------------------------

Declare @Debug bit

Set @Debug = 0

---------------------------------------------------- DEBUG ------------------------------------------------------


If @Debug = 1
	SET NOCOUNT OFF
ELSE
	SET NOCOUNT ON

-- Pre-Run House Cleaning
DROP TABLE IF EXISTS #ColumnNames
DROP TABLE IF EXISTS #OrderedColumns
DROP TABLE IF EXISTS #OrderedColumnsWithDataTypes
DROP TABLE IF EXISTS #SelectFormat
DROP TABLE IF EXISTS #FinalResults
DROP TABLE IF EXISTS ##PivotDrop
DROP TABLE IF EXISTS ##PivotFlattened
DROP TABLE IF EXISTS ##FormatPivotResults
	

-- Getting Imported Column Names
SELECT DISTINCT ColumnName
INTO #ColumnNames
FROM [dbo].[VariableData]

-- Setting up Dynamic SQL to Pull the Order of the Columns as they appeared on the sheet.
DECLARE @ColumnOrderCommand VARCHAR(1000)
DECLARE @total VARCHAR(12);

SET @total = (
		SELECT Count(1) AS Total
		FROM #ColumnNames
		)
SET @ColumnOrderCommand = 'SELECT Top ' + @total + ' ColumnName FROM [dbo].[VariableData] Order By [VariableDataID]'

If @Debug = 1
	BEGIN
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
		Print 'Comment: Just getting the count of columns, outside of the 3 hard defined columns.'
		Print @ColumnOrderCommand
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
	END

CREATE TABLE #OrderedColumns (
	OrderedColumnsID INT Identity(1, 1) PRIMARY KEY NOT NULL
	,ColumnName VARCHAR(255)
	)

INSERT INTO #OrderedColumns
EXEC (@ColumnOrderCommand)


Alter Table #OrderedColumns Add DataType varchar(40)
Update oc
SET oc.DataType = vdt.DataType
FROM #OrderedColumns oc
JOIN dbo.VariableDataType vdt
	on oc.ColumnName = vdt.ColumnName
--SELECT * FROM #OrderedColumns


-- Getting Dynamic Column List
DECLARE @RAWColumns_SELECT VARCHAR(MAX);
DECLARE @Columns_SELECT VARCHAR(MAX);

SET @RAWColumns_SELECT = (
		SELECT STUFF((
					SELECT '], [' + o1.ColumnName 
					FROM #OrderedColumns o1
					--ORDER BY o1.AccountKey
					FOR XML PATH('')
					), 1, 1, '')
		)
SET @Columns_SELECT = Stuff(@RAWColumns_SELECT, 1, 2, '') + ']'

If @Debug = 1
	BEGIN
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
		Print 'Comment: Creating a basic column list.'
		Print @Columns_SELECT
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
	END		

-- Getting Dynamic Columns with Data Types
DECLARE @RAWColumns_INSERT VARCHAR(8000);
DECLARE @Columns_INSERT VARCHAR(7500);

SET @RAWColumns_INSERT = (
		SELECT STUFF((
					SELECT ', [' + o1.ColumnName + '] nvarchar(255)'
					FROM #OrderedColumns o1
					--ORDER BY o1.AccountKey
					FOR XML PATH('')
					), 1, 1, '')
		)
SET @Columns_INSERT = Stuff(@RAWColumns_INSERT, 1, 1, '') -- + ']'

If @Debug = 1
	BEGIN
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
		Print 'Comment: Adding the generic datatype to all fields in the basic list.'
		Print @Columns_INSERT
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'	
	END

DECLARE @CreatePivotDrop_1 VARCHAR(MAX);
DECLARE @CreatePivotDrop_2 VARCHAR(MAX);
DECLARE @CreatePivotDrop_3 VARCHAR(MAX);
SET @CreatePivotDrop_1 = 'Create Table ##PivotDrop '
SET @CreatePivotDrop_2 = '([VariableDataID] bigint Primary Key, [GroupNumber] varchar(255),[Batch] varchar(255), [AccountKey] varchar(255), '
SET @CreatePivotDrop_3 = '' + @Columns_INSERT + ')'


If @Debug = 1
	BEGIN	
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
		Print 'Comment: Create table script for generic columns. This is the table the Pivoted data is going to drop into.'
		Print @CreatePivotDrop_1
		Print @CreatePivotDrop_2
		Print @CreatePivotDrop_3	
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
	END

EXEC (@CreatePivotDrop_1 + @CreatePivotDrop_2 + @CreatePivotDrop_3)


DECLARE @PivotCommand_1 varchar(MAX)
DECLARE @PivotCommand_2 varchar(MAX)
DECLARE @PivotCommand_3 varchar(MAX)
DECLARE @PivotCommand_4 varchar(MAX)
SET @PivotCommand_1 = 'Select [VariableDataID], [GroupNumber], [BATCH], [AccountKey], ' + @Columns_SELECT 
SET @PivotCommand_2 = 'From [dbo].[VariableData] ' 
SET @PivotCommand_3 = 'pivot (max(ColumnDetail) for ' 
SET @PivotCommand_4 = 'ColumnName in ( '+ @Columns_SELECT +' )) as OutputValues'


If @Debug = 1
	BEGIN
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
		Print 'Comment: Pivoting the data from vertical columns to horizontal.'
		Print @PivotCommand_1
		Print @PivotCommand_2
		Print @PivotCommand_3
		Print @PivotCommand_4
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
	END


Insert Into ##PivotDrop
EXEC (@PivotCommand_1 + @PivotCommand_2 + @PivotCommand_3 + @PivotCommand_4)
--SELECT * FROM ##PivotDrop


DECLARE @CreatePivotFlatten_1 VARCHAR(MAX);
DECLARE @CreatePivotFlatten_2 VARCHAR(MAX);
DECLARE @CreatePivotFlatten_3 VARCHAR(MAX);
DECLARE @CreatePivotFlatten_4 VARCHAR(MAX);
SET @CreatePivotFlatten_1 = 'Create Table ##PivotFlattened ' 
SET @CreatePivotFlatten_2 = '([VariableDataID] bigint IDENTITY (1,1) Primary Key NOT NULL, '
SET @CreatePivotFlatten_3 = '[GroupNumber] varchar(255),[Batch] varchar(255), [AccountKey] varchar(255), '
SET @CreatePivotFlatten_4 =  + @Columns_INSERT + ')'

If @Debug = 1
	BEGIN
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
		Print 'Comment: Each account had a row for each column with only that columns data in it. This is to create a table to help bring them into one row each.'
		Print @CreatePivotFlatten_1
		Print @CreatePivotFlatten_2
		Print @CreatePivotFlatten_3
		Print @CreatePivotFlatten_4
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
	END

EXEC (@CreatePivotFlatten_1 + @CreatePivotFlatten_2 + @CreatePivotFlatten_3 + @CreatePivotFlatten_4)


-- Getting Dynamic Column List
DECLARE @RAWColumns_FLATTEN VARCHAR(MAX);
DECLARE @Columns_FLATTEN VARCHAR(MAX);

SET @RAWColumns_FLATTEN = (
		SELECT STUFF((
					SELECT ']), MAX([' + o1.ColumnName
					FROM #OrderedColumns o1
					--ORDER BY o1.AccountKey
					FOR XML PATH('')
					), 1, 1, '')
		)
SET @Columns_FLATTEN = Stuff(@RAWColumns_FLATTEN, 1, 3, '') + '])'


If @Debug = 1
	BEGIN
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
		Print 'Comment: This is bulding out the columns string to flatten the data with. This is similar to a previous version, but this has an aggregate function in it.'
		Print @Columns_FLATTEN
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
	END

--SELECT @Columns_FLATTEN


DECLARE @FlattenPivot_1 VARCHAR(MAX);
DECLARE @FlattenPivot_2 VARCHAR(MAX);
DECLARE @FlattenPivot_3 VARCHAR(MAX);
DECLARE @FlattenPivot_4 VARCHAR(MAX);
SET @FlattenPivot_1 = 'SELECT [GroupNumber], [BATCH], [ACCOUNTKEY], '
SET @FlattenPivot_2 = + @Columns_FLATTEN + ''
SET @FlattenPivot_3 = 'FROM ##PivotDrop ' 
SET @FlattenPivot_4 = 'GROUP BY [GroupNumber], [BATCH], [ACCOUNTKEY]'

If @Debug = 1
	BEGIN
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
		Print 'Comment: Each account had a row for each column with only that columns data in it. This is to bring all of those rows into one.'
		Print @FlattenPivot_1
		Print @FlattenPivot_2
		Print @FlattenPivot_3
		Print @FlattenPivot_4
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
	END


Insert Into ##PivotFlattened
EXEC (@FlattenPivot_1 + @FlattenPivot_2 + @FlattenPivot_3 + @FlattenPivot_4)

--SELECT * FROM ##PivotFlattened


--Setting the Real Datatype for a column 
SELECT o1.ColumnName
	   ,CASE
            When o1.DataType = 'Money'
				then 'Money'
			When o1.DataType = 'Date'
				then 'Date'
            When o1.DataType = 'Percent'
				then 'Decimal(10,4)'
			Else 'varchar(255)'
		END as DataType  	 
Into #OrderedColumnsWithDataTypes 
FROM #OrderedColumns o1


--SELECT * From #OrderedColumnsWithDataTypes 



-- Getting Dynamic Columns with Data Types
DECLARE @RAWColumns_WithDataType VARCHAR(MAX);
DECLARE @Columns_WithDataType VARCHAR(MAX);

SET @RAWColumns_WithDataType = (
		SELECT STUFF((
SELECT ', [' + ocw.ColumnName + '] ' + ocw.DataType 
FROM #OrderedColumnsWithDataTypes ocw
					FOR XML PATH('')
					), 1, 1, '')
		)
SET @Columns_WithDataType = Stuff(@RAWColumns_WithDataType, 1, 1, '')


If @Debug = 1
	BEGIN
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
		Print 'Comment: Each account had a row for each column with only that columns data in it. This is to bring all of those rows into one.'
		Print @Columns_WithDataType
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
	END


DECLARE @FormatPivotResults_1 VARCHAR(MAX);
DECLARE @FormatPivotResults_2 VARCHAR(MAX);
DECLARE @FormatPivotResults_3 VARCHAR(MAX);
SET @FormatPivotResults_1 = 'Create Table ##FormatPivotResults '
SET @FormatPivotResults_2 = '(VariableDataID bigint IDENTITY (1,1) Primary Key NOT NULL , [GroupNumber] varchar(255),[Batch] varchar(255), [AccountKey] varchar(255),'
SET @FormatPivotResults_3 =  + @Columns_WithDataType + ')'

If @Debug = 1
	BEGIN
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
		Print 'Comment: Each account had a row for each column with only that columns data in it. This is to bring all of those rows into one.'
		Print @FormatPivotResults_1
		Print @FormatPivotResults_2
		Print @FormatPivotResults_3
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
	END

EXEC (@FormatPivotResults_1 + @FormatPivotResults_2 + @FormatPivotResults_3)


--Formatting the fields for the Select.
Declare @DecimalCase_1 VARCHAR(256)
Declare @DecimalCase_2 VARCHAR(256)
Declare @DecimalCase_3 VARCHAR(256)
Declare @DecimalCase_4 VARCHAR(256)
Declare @DecimalCase_5 VARCHAR(256)

SET @DecimalCase_1 = 'Try_convert(decimal(15,15), ['  -- ColumnName
SET @DecimalCase_2 = ']) AS [' -- ColumnName 
SET @DecimalCase_3 = '], CASE WHEN Try_convert(decimal(15,15), [' -- ColumnName
SET @DecimalCase_4 = ']) IS NULL THEN [' -- ColumnName 
SET @DecimalCase_5 = '] ELSE '''' END AS PreviousColumnConversionError'



SELECT ColumnName
	   ,CASE
            When DataType = 'Money'
				then 'Try_convert(money, [' + ColumnName + ']) AS [' + ColumnName + ']'
			When DataType = 'Date'
				then 'Dateadd(day,-2,DateAdd(day,Try_convert(int, [' + ColumnName + '] ),''1900/01/01'')) as [' + ColumnName + ']'
            When DataType = 'Percent'
				--then 'Try_convert(decimal(15,15), [' + ColumnName + ']) AS [' + ColumnName + ']'
				then (@DecimalCase_1 + ColumnName + @DecimalCase_2 + ColumnName + @DecimalCase_3 + ColumnName + @DecimalCase_4 + ColumnName + @DecimalCase_5)
			Else '[' + ColumnName + ']'
		END as SelectFormat
Into #SelectFormat		  	 
FROM #OrderedColumns

--SELECT * FROM #SelectFormat


-- Getting Dynamic Column List
DECLARE @SelectColumns_Format VARCHAR(MAX);
DECLARE @Columns_Format VARCHAR(MAX);

SET @SelectColumns_Format = (
		SELECT STUFF((
					SELECT ', ' + sf.SelectFormat
					FROM #SelectFormat sf
					--ORDER BY o1.AccountKey
					FOR XML PATH('')
					), 1, 1, '')
		)
SET @Columns_Format = Stuff(@SelectColumns_Format, 1, 1, '')


If @Debug = 1
	BEGIN
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
		Print 'Comment: Building the variable portion of the formating select.'
		Print @Columns_Format
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
	END


--SELECT @Columns_Format


-- 
DECLARE @FormatDrop VARCHAR(MAX);
SET @FormatDrop = ('SELECT [GroupNumber], [BATCH], [ACCOUNTKEY], ' + @Columns_Format + ' FROM ##PivotFlattened')

--SELECT @FormatDrop



If @Debug = 1
	BEGIN
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
		Print 'Comment: Building the variable portion of the formating select.'
		Print @FormatDrop
		Print '------------------------------------------------------------------------------------------------------------------------------------------------------------'
	END


--Insert Into ##FormatPivotResults
EXEC (@FormatDrop)
--*/
/*
SELECT * 
Into #FinalResults
FROM ##FormatPivotResults


Create Clustered Index IX_VariableDataID on #FinalResults
(
	[VariableDataID] ASC
)

Create NonClustered Index IX_AccountKey on #FinalResults
(
	[AccountKey] ASC
)


Select * from #FinalResults


-- Post Run House Cleaning
DROP TABLE IF EXISTS #ColumnNames
DROP TABLE IF EXISTS #OrderedColumns
DROP TABLE IF EXISTS #OrderedColumnsWithDataTypes
DROP TABLE IF EXISTS #SelectFormat
--DROP TABLE IF EXISTS #FinalResults
DROP TABLE IF EXISTS ##PivotDrop
DROP TABLE IF EXISTS ##PivotFlattened
DROP TABLE IF EXISTS ##FormatPivotResults
--*/

```