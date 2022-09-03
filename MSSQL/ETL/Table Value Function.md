# Table Value Function

Think of this like a view but you can directly send variables to it.

This one also as a section at the bottom to provide something back if no rows were in returned otherwise.

  

## Function Code:

```
CREATE FUNCTION dbo.ufn_AlbumsByGenre_MSTVF(@GenreId int)
RETURNS @Albums TABLE (
	ArtistName nvarchar(255),
	AlbumName nvarchar(255),
	Genre nvarchar(50)
	)
AS
BEGIN
    INSERT INTO @Albums
    SELECT 
        ar.ArtistName,
        al.AlbumName,
        g.Genre
    FROM Genres g 
        INNER JOIN Albums al
        ON g.GenreId = al.GenreId 
        INNER JOIN Artists ar 
        ON al.ArtistId = ar.ArtistId
    WHERE g.GenreId = @GenreId
 
    IF @@ROWCOUNT = 0
    BEGIN
        INSERT INTO @Albums
        VALUES (
            'None',
            'None',
            'None'
            )
    END

    RETURN
END
GO
```

  

Use Function:

```
SELECT * FROM dbo.ufn_AlbumsByGenre_MSTVF(45)

```