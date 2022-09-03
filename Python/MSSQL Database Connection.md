# MSSQL Database Connection

  

**requirements.txt**  

**

```
pyodbc

```

  

  

**In Line Python**  

```
# Importing the Acquired Modules
import pyodbc


def Database():

    try:
        conn = pyodbc.connect(r'Driver={SQL Server Native Client 11.0};Server=' + sql_server + ';Database=' + sql_database + ';Trusted_Connection=yes;')
        cursor = conn.cursor()

    except Exception as ex:
        Logging(ex)
        quit()

    return conn, cursor

```

  




**