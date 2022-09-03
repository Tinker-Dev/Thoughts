# Partition Load

```
CREATE PROCEDURE [dbo].[YOURTABLE_Partition_Load] (@ReportDate date)
AS
/*-------------------------------------------------------------------------------------------------------------------------------
        Declaration and initialization of variables.
-------------------------------------------------------------------------------------------------------------------------------*/
        DECLARE @PartitionError                                int
        DECLARE @PartitionError_Message                varchar(255)
        DECLARE @PartitionError_Line                int

/*-------------------------------------------------------------------------------------------------------------------------------
        This code block performs the necessary metadata switch of the partition from the stage table to that of the partitioned
        base table.  No data movement occurs as this is a metadata operation.
-------------------------------------------------------------------------------------------------------------------------------*/
        BEGIN TRY                                                
                BEGIN TRANSACTION
                        ALTER TABLE YOURDB.dbo.YOURTABLE_Stage
                                SWITCH PARTITION $PARTITION.pfn_YOURDB_Daily(@ReportDate)
                                        TO YOURDB.dbo.YOURTABLE PARTITION $PARTITION.pfn_YOURDB_Daily(@ReportDate)
                COMMIT TRANSACTION
        END TRY

/*-------------------------------------------------------------------------------------------------------------------------------
        This code block performs the error handling of the stored procedure in the event of an error.
-------------------------------------------------------------------------------------------------------------------------------*/
        BEGIN CATCH
                SELECT  @PartitionError                        = ERROR_NUMBER(),
                                @PartitionError_Message        = ERROR_MESSAGE(),
                                @PartitionError_Line        = ERROR_LINE()
         
                SET @PartitionError_Message = 'Partition stored procedure failed with error %d at line %d: ' + @PartitionError_Message
                        RAISERROR(@PartitionError_Message, 16, 1, @PartitionError, @PartitionError_Line) WITH NOWAIT
         
                IF @@TRANCOUNT > 0
                        BEGIN
                                ROLLBACK
                        END
        END CATCH
GO

```