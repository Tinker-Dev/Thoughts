# Dynamic Pivot

There are plenty of times that you need to pivot data, so here is the query to do it.  

```
DECLARE
V_VALUES LONG;
V_QUERY LONG;
BEGIN

-- Concating pivot column name using a comma then replacing "'" with "''" because we will use it in a dynamic query and "'" can effect query.
  SELECT DISTINCT 
         LISTAGG(to_char('''' || REPLACE(your_pivot_column,'''','''''') || ''''),',')
         WITHIN GROUP (ORDER BY your_pivot_column) AS temp_in_statement 
    INTO V_VALUES
    FROM (SELECT DISTINCT your_pivot_column FROM your_table);

-- Building the Dynamic Query  
  V_QUERY := 'CREATE TABLE your_output_table 
                AS
                SELECT * 
                FROM (SELECT column1,column2,your_pivot_column,your_pivot_content 
                          FROM your_table)   
                PIVOT (max(your_pivot_content) FOR your_pivot_column IN 
                     (' ||V_VALUES|| '))  
                ORDER BY column1, column2' ;

dbms_output.put_line(V_QUERY);
EXECUTE IMMEDIATE V_QUERY;

END;

```