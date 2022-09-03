# Results to Comma Separated String

When you are start working with Dynamic SQL and things of that nature you may find yourself needing the results of a query to be returned as comma separated values. The example here is finds values from one query to used them in as column values in another.  

  

```
DECLARE @RAWColumns VARCHAR(MAX);
DECLARE @Columns VARCHAR(MAX);

SET @RAWColumns = (
		SELECT STUFF((
					SELECT ']), MAX([' + o1.ColumnName
					FROM #OrderedColumns o1
					FOR XML PATH('')
					), 1, 1, '')
		)
SET @Columns = Stuff(@RAWColumns, 1, 3, '') + '])'

```