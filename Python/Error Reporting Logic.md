# Error Reporting Logic

Nobody either codes perfectly or can anticipate everything. So there are going to be errors from time to time. The idea here is to soften the blow for when one of those occurs.  

  

```
while True:
    try:
        n = input("Please enter an integer: ")
        n = int(n)
        break
    except ValueError:
        print("No valid integer! Please try again ...")
print("Great, you successfully entered an integer!")

```