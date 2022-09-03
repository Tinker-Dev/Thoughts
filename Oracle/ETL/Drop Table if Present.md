# Drop Table if Present

Here is the code to drop a table if it is present.  

```
DECLARE
   c int;
BEGIN
   SELECT COUNT(*) INTO c FROM user_tables WHERE table_name = UPPER('your_holding_table');
   IF c = 1 THEN
      EXECUTE IMMEDIATE 'DROP TABLE your_holding_table';
   END IF;
END;
/

```