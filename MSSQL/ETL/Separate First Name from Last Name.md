# Separate First Name from Last Name

```
SELECT
  SUBSTRING(FullName, 1, CHARINDEX(' ', FullName) - 1) AS FirstName,
  REVERSE(SUBSTRING(REVERSE(FullName), 1, CHARINDEX(' ', REVERSE(FullName)) - 1)) AS LastName
FROM
  [PERSON_TABLE]

```