# What Character Set is the Database In?

If the Character set doesn’t meet your expectations then you might have issues. I have was having an issues where results where wider than they appeared. As in you would have to arrow twice to make the cursor advance one letter. There were other signs as well. I was expecting UTF-8, but the system was in UTF-16. This was causing issues with a LISTAGG output.

  

Here is the code to see what Character Set is being used.  

```
SELECT * FROM nls_database_parameters WHERE parameter = 'NLS_NCHAR_CHARACTERSET';

```