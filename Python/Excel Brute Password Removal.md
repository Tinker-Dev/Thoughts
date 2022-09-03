# Excel Brute Password Removal

- From time to time you will be in the place where you have lost the password for an Excel file. Here is a tool that will help you overcome that. This is time consuming.
    
- _**Note:**_ This process is only as good as your word list.
    

  

## Code

```

import sys
import win32com.client
openedDoc = win32com.client.Dispatch("Excel.Application")
filename= sys.argv[1]

password_file = open ( 'wordlist.lst', 'r' )
passwords = password_file.readlines()
password_file.close()

passwords = [item.rstrip('\n') for item in passwords]

results = open('results.txt', 'w')

for password in passwords:
	print(password)
	try:
		wb = openedDoc.Workbooks.Open(filename, False, True, None, password)
		print("Success! Password is: "+password)
		results.write(password)
		results.close()
	except:
		print("Incorrect password")
		pass

```

  

  

## Word List

- The code requires a word list. If you are in need of one to get you started you can find one here.

    - [https://www.kali.org/tools/wordlists/](https://www.kali.org/tools/wordlists/)