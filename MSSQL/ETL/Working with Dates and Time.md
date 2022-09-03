# Working with Dates and Time

## Breaking Down by Hour

```
SELECT	 CAST(startTime AS date) AS ForDate
		,DATEPART(HOUR, startTime) AS OnHour
		,COUNT(*) AS Totals
FROM	 Table1
WHERE	 startTime >= '2017-08-14 00:00:00.000'
		 AND startTime <= '2017-08-15 00:00:00.000'
GROUP BY CAST(startTime AS date)
		,DATEPART(HOUR, startTime)
ORDER BY OnHour;

```

  

## Beginning and End of the Month

```
DECLARE        @reportdate DATE;
DECLARE        @BeginDate DATE;
DECLARE        @EndDate DATE;

SET @reportdate = DATEADD(MONTH, DATEDIFF(MONTH, 0, GETDATE()) - 1, 0);
SET @BeginDate = DATEADD(MONTH, DATEDIFF(MONTH, 0, GETDATE()) - 1, 0);
SET @EndDate = DATEADD(MONTH, DATEDIFF(MONTH, -1, GETDATE()) - 1, -1);



SELECT @reportdate
SELECT @BeginDate
SELECT @EndDate


GO
------------------------------------------------------------------------------------------


DECLARE @MasterDate date
SET @MasterDate = DATEADD(DAY,360,GETDATE())


-- Calculating Working Dates
DECLARE @ReportDate date
                ,@EndOfYearDaily date
                ,@EndOfYearMonthly date

SET @ReportDate = DATEADD(DAY,-1,@MasterDate)        
SET @EndOfYearDaily = DATEADD(Year, DATEDIFF(YEAR, -1, @MasterDate) -1, -1)
SET @EndOfYearMonthly = DATEADD(MONTH, DATEDIFF(MONTH, 0, @EndOfYearDaily), 0)


SELECT @ReportDate, @EndOfYearDaily, @EndOfYearMonthly


GO

```

  

## Date to Varchar

```
DECLARE @Example decimal;
SET @Example = 19460101;
SELECT CAST(STUFF(STUFF(@Example, 5, 0, '-'), 8, 0, '-') AS datetime);

```

  

## In the Last 12 Months

Set to bring back a revolving last 12 months of data  

```
DECLARE @SourceDate date;
DECLARE @FirstDay varchar(11);
DECLARE @LastDay varchar(11);

SET @SourceDate = '02/01/2016';
SET @FirstDay = CONVERT(varchar(11), DATEADD(mm, DATEDIFF(mm, 0, DATEADD(mm, -12, @SourceDate)), 0));
SET @LastDay = CONVERT(varchar(11), DATEADD(s, -1, DATEADD(mm, DATEDIFF(m, 0, @SourceDate), 0)));


SELECT @FirstDay AS [12 Months Ago]
	  ,@LastDay AS [Last Day of Last Month];

```

  

## Julian to Gregorian Date

```
CONVERT(char(10),DATEADD(DAY,CONVERT(int,RIGHT(JULIAN_DATE,3)),'01/01/'+LEFT(RIGHT(JULIAN_DATE ,5),2))-1,101)

```