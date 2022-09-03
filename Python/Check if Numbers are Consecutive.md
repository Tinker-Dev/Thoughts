# Check if Numbers are Consecutive

```
def checkConsecutive(l):
	return sorted(l) == list(range(min(l), max(l)+1))
  
numberlist = [2, 3, 1, 4, 5]
print(checkConsecutive(numberlist))

```