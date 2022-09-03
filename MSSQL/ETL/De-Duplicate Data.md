# De-Duplicate Data

```
-- Find the Dups
    select *
    from (
      select *, rn=row_number() over (partition by Item1, Item2, Item3, Item4 order by Item1 )
      from Table1
    ) x
    where rn > 1;


-- Delete Dups
    BEGIN TRAN
    delete x from (
      select *, rn=row_number() over (partition by Item1, Item2, Item3, Item4 order by Item1 )
      from Table1
    ) x
    where rn > 1;
    
    
    --COMMIT TRAN
    --ROLLBACK TRAN

```