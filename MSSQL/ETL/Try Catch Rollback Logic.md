# Try, Catch, Rollback Logic

When you are needing logic in place that can back out any changes you just made if something goes wrong.  

  

## Simple

```
BEGIN TRY
    BEGIN TRANSACTION
    --================================================
    -- Add Your Code Here
    --================================================
    COMMIT
END TRY
BEGIN CATCH
 ROLLBACK
END CATCH

```

  

  

## A Little More Complex

```
DECLARE @test int
 
BEGIN TRANSACTION;   
BEGIN TRY
  
   SET @test = 'A'
 
   SELECT   @@TRANCOUNT;       
   IF @@TRANCOUNT > 0
      BEGIN
         COMMIT TRANSACTION;
      END;
END TRY   
BEGIN CATCH
   IF @@TRANCOUNT > 0
      BEGIN
         ROLLBACK TRANSACTION;
         RAISERROR('Your process failed.',16,1);
         RETURN;
      END;
END CATCH;

```