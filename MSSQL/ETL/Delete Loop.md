# Delete Loop

```
IF OBJECT_ID('tempdb..#WorkTable') IS NOT NULL
		DROP TABLE #WorkTable;
IF OBJECT_ID('tempdb..#BadIDs') IS NOT NULL
		DROP TABLE #BadIDs;


SELECT	ID
INTO	#WorkTable
FROM	[YOURDB].[dbo].[YOURTABLE]
WHERE	ID IN ();



CREATE TABLE #BadIDs (BadID int);


DECLARE	@IDToDelete int;

looppoint:

BEGIN TRY
		WHILE (
				SELECT	COUNT(1)
				FROM	#WorkTable
				) > 0
				BEGIN
						

						SET @IDToDelete = (
											SELECT TOP 1
														ID
											FROM		#WorkTable
											);

						DELETE	[YOURDB].[dbo].[YOURTABLE]
						WHERE	ID = @IDToDelete;
 
						DELETE	#WorkTable
						WHERE	ID = @IDToDelete;
				END;

END TRY

BEGIN CATCH

		INSERT	INTO #BadIDs
				(BadID)
		VALUES	(@IDToDelete);

		DELETE	#WorkTable
		WHERE	ID = @IDToDelete;

		GOTO looppoint;

END CATCH;




SELECT ID FROM #WorkTable   -- Should be Blank
SELECT BadID FROM #BadIDs	-- Hope Is Blank

```