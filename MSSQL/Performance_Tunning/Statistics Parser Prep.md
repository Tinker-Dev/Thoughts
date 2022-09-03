# Statistics Parser Prep

```
-- You can use statisticsparser.com to break things down.

--- SPECIAL Plan
SET STATISTICS IO ON 
SET STATISTICS TIME ON 

--- CODE HERE

SET STATISTICS IO OFF 
SET STATISTICS TIME OFF   

 

 

--Update Statistics
USE DATABASE_NAME;
GO   

UPDATE STATISTICS dbo.TABLE_NAME(STATISTIC_NAME) WITH FULLSCAN, NORECOMPUTE;   
GO

```