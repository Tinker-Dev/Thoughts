# Get Monday's Date

```
WITH ids
AS (
	SELECT TOP (14)
    id = ROW_NUMBER() OVER (ORDER BY (SELECT 1)) - 7
    FROM sys.columns
	),
   
alldates
AS (
	SELECT dates        = DATEADD(DAY, ids.id, CONVERT(DATE, GETDATE()))
			 , week_num     = DATEPART(WEEK, DATEADD(DAY, ids.id, CONVERT(DATE, GETDATE())))
			 , day_of_week  = DATENAME(WEEKDAY, DATEADD(DAY, ids.id, CONVERT(DATE, GETDATE())))
			 , current_week = DATEPART(WEEK, GETDATE())
		  FROM ids
	)

SELECT alldates.dates
  FROM alldates
 WHERE alldates.week_num = alldates.current_week
       AND alldates.day_of_week = 'Monday';

```