# Fix Percent Signs in Data

Here is a query to find a Percent Sign in your data.  

```
SELECT TOP (100) [VariableDataID]
      ,[GroupNumber]
      ,[Batch]
      ,[AccountKey]
      ,[ColumnName]
      ,[ColumnDetail]
  FROM [Consume].[dbo].[VariableData]
  Where ColumnDetail like '%.[%]%'

```