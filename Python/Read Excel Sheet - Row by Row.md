# Read Excel Sheet - Row by Row

There are plenty of times when you may need to read an Excel sheet. You can do this with the code below. Note: This works well for a sheet that only has a few thousand rows. After that the performance starts to take a hit.  

  

## The Python Code:

```
# For reading the Excel sheet that functions as the queue. 
import xlrd

# Defining parameters to be used in function call.
readfile = '.\\TestSheet.xlsx' 
sheetname = 'Sheet1'



# This function is reading the values in the Excel sheet and appling them to 
# Granting function.        
def MainProcess(READFILE,READSHEET):  
    # Start reading the Sheet.
    workbook = xlrd.open_workbook(READFILE)            
    worksheet = workbook.sheet_by_name(READSHEET)
    # Counting the data rows, so minus the header.
    num_rows = worksheet.nrows - 1
    curr_row = 0
    #Starting the processing loop.
    while curr_row < num_rows:
            # Setting the next line to +=1 will have it skip the header row.
            curr_row += 1
            row = worksheet.row(curr_row)            
            # Build out the values to supply to the permissions function.
            ReadColumn1 = row[0].value
            ReadColumn2 = row[1].value
            ReadColumn3 = row[2].value                       
            # Running the Grant function with the needed parameters.
            print (ReadColumn1 + ', ' + ReadColumn2 + ', ' + ReadColumn3)
            
            
            
# Calling the Function
MainProcess(readfile,sheetname) 

The Sheet Layout:

```