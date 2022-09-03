# Find a Bad Conversion

```
SELECT [Row ID],
        Try_convert(date, [INVOICE DATE]) AS [INVOICE DATE],
        CASE
            WHEN Try_convert(date, [INVOICE DATE]) IS NULL THEN 'BAD'
            ELSE 'good'
        END AS comments
FROM   test_tab

```