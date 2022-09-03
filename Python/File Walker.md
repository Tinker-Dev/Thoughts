# File Walker

```

# Provide Base Folder
sourcepath = r''

for root, dirs, files in os.walk(sourcepath):
	for file_name in files:
		if file_name.lower().endswith(".txt"):
			if os.path.exists(root + '\\' +file_name) == True:
				print(root + '\\' + file_name)

```