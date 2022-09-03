# Vertical Storage Pivot

If you have data stored vertically in a “key, value” pair then this is something that could help you look at it horizontally.  

  

```
USE [TestDB]
GO

-- TRUNCATE TABLE [dbo].[VariableData]
-- SELECT * FROM [dbo].[VariableData]
-- Pre-Run House Cleaning
DROP TABLE IF EXISTS #ColumnNames
DROP TABLE IF EXISTS #OrderedColumns
DROP TABLE IF EXISTS ##PivotDrop
DROP TABLE IF EXISTS ##PivotFlattened


-- Getting Imported Column Names
SELECT DISTINCT ColumnName
INTO #ColumnNames
FROM [dbo].[VariableData]

-- Settting up Dynamic SQL to Pull the Order of the Columns as they appeared on the sheet.
DECLARE @ColumnOrderCommand VARCHAR(1000)
DECLARE @total VARCHAR(12);

SET @total = (
		SELECT Count(1) AS Total
		FROM #ColumnNames
		)
SET @ColumnOrderCommand = 'SELECT Top ' + @total + ' ColumnName FROM dbo.VariableData Order By VariableDataID'

CREATE TABLE #OrderedColumns (
	OrderedColumnsID INT Identity(1, 1) PRIMARY KEY NOT NULL
	,ColumnName VARCHAR(255)
	)

INSERT INTO #OrderedColumns
EXEC (@ColumnOrderCommand)

-- Getting Dynamic Column List
DECLARE @RAWColumns_SELECT VARCHAR(8000);
DECLARE @Columns_SELECT VARCHAR(7500);

SET @RAWColumns_SELECT = (
		SELECT STUFF((
					SELECT '], [' + o1.ColumnName
					FROM #OrderedColumns o1
					--ORDER BY o1.AccountKey
					FOR XML PATH('')
					), 1, 1, '')
		)
SET @Columns_SELECT = Stuff(@RAWColumns_SELECT, 1, 2, '') + ']'

--SELECT @Columns_SELECT


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
SET @Columns_INSERT = Stuff(@RAWColumns_INSERT, 1, 1, '') + ']'

DECLARE @StringLEN INT

SET @StringLEN = (
		SELECT LEN(@Columns_INSERT)
		)

--Select @StringLEN
DECLARE @Cleaned_Columns_INSERT VARCHAR(7500)

SET @Cleaned_Columns_INSERT = (
		SELECT Stuff(@Columns_INSERT, @StringLEN, 1, '')
		)

--SELECT @Cleaned_Columns_INSERT


DECLARE @CreatePivotDrop VARCHAR(8000);
SET @CreatePivotDrop = ('Create Table ##PivotDrop (VariableDataID bigint Primary Key, GroupNumber varchar(255),Batch varchar(255), AccountKey varchar(255), ' + @Cleaned_Columns_INSERT + ')')
EXEC (@CreatePivotDrop)


DECLARE @PivotCommand varchar(8000)
SET @PivotCommand = 'Select * From [dbo].[VariableData] pivot (max(ColumnDetail) for ColumnName in ( '+ @Columns_SELECT +' )) as OutputValues'


Insert Into ##PivotDrop
EXEC (@PivotCommand)


DECLARE @CreatePivotFlatten VARCHAR(8000);
SET @CreatePivotFlatten = ('Create Table ##PivotFlattened (VariableDataID bigint IDENTITY (1,1) Primary Key NOT NULL , GroupNumber varchar(255),Batch varchar(255), AccountKey varchar(255), ' + @Cleaned_Columns_INSERT + ')')
EXEC (@CreatePivotFlatten)

--SELECT * FROM ##PivotDrop



-- Getting Dynamic Column List
DECLARE @RAWColumns_FLATTEN VARCHAR(8000);
DECLARE @Columns_FLATTEN VARCHAR(7500);

SET @RAWColumns_FLATTEN = (
		SELECT STUFF((
					SELECT ']), min([' + o1.ColumnName
					FROM #OrderedColumns o1
					--ORDER BY o1.AccountKey
					FOR XML PATH('')
					), 1, 1, '')
		)
SET @Columns_FLATTEN = Stuff(@RAWColumns_FLATTEN, 1, 2, '') + '])'

--SELECT @Columns_FLATTEN



DECLARE @FlattenPivot VARCHAR(8000);
SET @FlattenPivot = ('SELECT GROUPNUMBER, BATCH, ACCOUNTKEY, ' + @Columns_FLATTEN + ' FROM ##PivotDrop GROUP BY GROUPNUMBER, BATCH, ACCOUNTKEY')

Insert Into ##PivotFlattened
EXEC (@FlattenPivot)

SELECT * FROM ##PivotFlattened



/*
-- Post Run House Cleaning
DROP TABLE IF EXISTS #ColumnNames
DROP TABLE IF EXISTS #OrderedColumns
DROP TABLE IF EXISTS ##PivotDrop
DROP TABLE IF EXISTS ##PivotFlattened
--*/

```