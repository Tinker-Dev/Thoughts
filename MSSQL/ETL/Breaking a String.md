# Breaking a String

```
SELECT TOP 100 bs.Addr_Line_1 AS Baseline
	  ,CHARINDEX(' ', bs.Addr_Line_1) AS [Count to First Space]
	  ,LEFT(bs.Addr_Line_1, CHARINDEX(' ', bs.Addr_Line_1)) AS [Everything before the space]
	  ,RIGHT(bs.Addr_Line_1, LEN(bs.Addr_Line_1) - CHARINDEX(' ', bs.Addr_Line_1)) AS [Everything after the space]
FROM   Table1;

```