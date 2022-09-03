# PYODBC Sample

This is just a quick sample of how to setup PYODBC. This code has been tailored to connect to a MS SQL database.  

  

**

```

# Since pyodbc is not a native package, this will try to import if it is not present on the system.
try:
	import pyodbc
except ImportError:
	from pip._internal import main as pip
	pip(['install','pyodbc','--user'])
	import pyodbc


# Global Variables
gv_schema = 'dbo'
gv_table = 'TestSample'

gv_schematable = gv_schema + "." + gv_table

# Function to Connect to the ODBC
def DatabaseConnect():
	conn = pyodbc.connect('DSN=NAME OF ODBC')
	cursor = conn.cursor()
	return conn, cursor


# Function to create a table
def CreateTable():
	# Check to see if the table is present.
	table_present_check = ("SELECT Count(*) AS Result FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_SCHEMA = '" + gv_schema + "' and TABLE_NAME = '" + gv_table + "'"))

	# Use the function to create a DB connection
	conn, cursor = DatabaseConnect()
	
	# Execute the SQL.
	cursor.execute(table_present_check)

	# Reading the data returned from the query
	rawfetch = cursor.fetchone()

	# If the table was not present create it else move on
	if rawfetch[0] == 0:
		# Build the SQL script
		create_table_scripts = ("CREATE TABLE " + gv_schematable + " (ID BigINT Identity(1,1) NOT NULL, SampleColumnOne nvarchar(20) NOT NULL, SampleColumnTwo nvarchar(10) NULL")

	# Use the function to create a DB connection
	conn, cursor = DatabaseConnect()

	# Execute the SQL.
	cursor.execute(create_table_scripts)

	# Commit the Transaction
	conn.commit()
	
	# Adding an Index to help with things
	create_index_script = ("CREATE UNIQUE CLUSTERED INDEX [ix_SampleIX] ON " + gv_schematable + "([SampleColumnOne] ASC")

	# Use the function to create a DB connection
	conn, cursor = DatabaseConnect()

	# Execute the SQL.
	cursor.execute(create_index_script)

	# Commit the Transaction
	conn.commit()


# This is a function to write to the table. Now there are a couple of ways to do this, however this is one of the "safe" ways to do it. It keep SQL injection opportunities at a minimum.
def WriteToTable(def_Value1, def_Value2):

	# Use the function to create a DB connection
	conn, cursor = DatabaseConnect()
	
	# Creating the SQL statement
	FUNC_TEMPLATE_INSERT = """cursor.execute(INSERT INTO {0} (SampleColumnOne, SampleColumnTwo) values ('{1},'{2}')")"""
	
	# Executing the SQL
	exec(FUNC_TEMPLATE_INSERT.format(gv_schematable,def_Value1, def_Value2))

	# Commit the Transaction
	conn.commit()	


# Read one row of data
def ReadOne():
	# Use the function to create a DB connection
	conn, cursor = DatabaseConnect()

	# A should only return one row so we only expect one result.
	read_table = ("SELECT Count(*) AS Result FROM " + gv_schematable)

	# Execute the SQL.
	cursor.execute(read_table)

	# Reading the data returned from the query
	rawfetch = cursor.fetchone()

	print(rawfetch)

	
# Read more than one row of data
def ReadMany():
	# Use the function to create a DB connection
	conn, cursor = DatabaseConnect()

	# A should only return one row so we only expect one result.
	read_table = ("SELECT SampleColumnOne, SampleColumnTwo FROM " + gv_schematable)

	# Execute the SQL.
	cursor.execute(read_table)

	# Reading the data returned from the query
	rows = cursor.fetchall()
	for row in rows
		print(row)
	



# Kicking Everything Off
print('Creating the table')
CreateTable()

print('Adding some data to the table.')
WriteToTable('Breakfast', '7am')
WriteToTable('Second Breakfast', '9am')
WriteToTable('Elevenses', '11am')
WriteToTable('Lunchoen', '12pm')
WriteToTable('Afternoon Tea', '3pm')
WriteToTable('Dinner', '5pm')
WriteToTable('Supper', '7pm')

print('Counting the Rows')
ReadOne()

print('Viewing the rows')
ReadMany()

```

  


**