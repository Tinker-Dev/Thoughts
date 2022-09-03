# Hidden Character Check

```
SELECT DATALENGTH(IdentityProviderName) AS Data_Length
	  ,LEN(IdentityProviderName) AS Text_Length
FROM   TABLE_NAME;

```