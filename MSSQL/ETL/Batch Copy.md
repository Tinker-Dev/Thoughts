# Batch Copy

```
----------------------------------- Setting the Stage -----------------------------------
-- Cleaning House
IF OBJECT_ID('tempdb..#Source') IS NOT NULL DROP TABLE #Source
IF OBJECT_ID('tempdb..#Destination') IS NOT NULL DROP TABLE #Destination
IF OBJECT_ID('tempdb..#WorkingList') IS NOT NULL DROP TABLE #WorkingList
IF OBJECT_ID('tempdb..#SubWorkingList') IS NOT NULL DROP TABLE #SubWorkingList


-- Creating source data to work with.
CREATE TABLE #Source (SourceID int IDENTITY,MonthNo int, Month char(9), Season char(6)) 
    INSERT INTO #Source (MonthNo,Month,Season) VALUES ('4','April','Spring')
    INSERT INTO #Source (MonthNo,Month,Season) VALUES ('8','August','Summer')
    INSERT INTO #Source (MonthNo,Month,Season) VALUES ('12','December','Winter')
    INSERT INTO #Source (MonthNo,Month,Season) VALUES ('2','February','Winter')    
    INSERT INTO #Source (MonthNo,Month,Season) VALUES ('1','January','Winter') 
    INSERT INTO #Source (MonthNo,Month,Season) VALUES ('7','July','Summer')
    INSERT INTO #Source (MonthNo,Month,Season) VALUES ('6','June','Summer')
    INSERT INTO #Source (MonthNo,Month,Season) VALUES ('3','March','Spring')
    INSERT INTO #Source (MonthNo,Month,Season) VALUES ('5','May','Spring')
    INSERT INTO #Source (MonthNo,Month,Season) VALUES ('11','November','Fall')
    INSERT INTO #Source (MonthNo,Month,Season) VALUES ('10','October','Fall')
    INSERT INTO #Source (MonthNo,Month,Season) VALUES ('9','September','Fall')
           
-- Creating source data to work with.
CREATE TABLE #Destination (DestID int IDENTITY,MonthNo int, Month char(9), Season char(6)) 


----------------------------------- Beginning of Example -----------------------------------

-- Getting an key column (Identity) of all the records I am wanting to batch.
SELECT SourceID
INTO #WorkingList 
FROM #Source
GO

-- Defining the Loop Size and Count 
-- The "2" is how many records I am going to work at a time. 
-- The "+1" is to add an extra loop to catch the leftovers that are not divisible by your loop size.
DECLARE @LoopCount int
SET @LoopCount = (SELECT COUNT(SourceID) / 2 + 1
                  FROM #WorkingList)

-- Defining the Loop			   
WHILE @LoopCount > 0 
      BEGIN
		  -- Defining the exact records you are going to work this loop.
		  -- Again, the "2" is your Loop Size. 
		  SELECT TOP 2 SourceID
		  INTO #SubWorkingList 
		  FROM #WorkingList
		  
		  -- Just a normal query that joins to your SubWorkList
		  -- to know which records you are working with.
            INSERT INTO #Destination (MonthNo,Month,Season)
		  SELECT MonthNo,Month,Season 
		  FROM #Source so
		  INNER JOIN #SubWorkingList sw
			 ON so.SourceID = sw.SourceID
		  
		  -- Now you are deleting from the Worklist the records that match your
		  -- SubWorkList. This is so you don't grab the same row from the source again.
		  DELETE wl 
		  FROM #WorkingList wl
		  INNER JOIN #SubWorkingList sw
			 ON wl.SourceID = sw.SourceID
		  
		  -- Deleting the SubWorkList so that you can create it in the next loop.	
		  IF OBJECT_ID('tempdb..#SubWorkingList') IS NOT NULL DROP TABLE #SubWorkingList
		  		  		              
		  SET @LoopCount = @LoopCount - 1
      END
GO

-- Checking End Results.
SELECT * 
FROM #Destination
ORDER BY MonthNo

```