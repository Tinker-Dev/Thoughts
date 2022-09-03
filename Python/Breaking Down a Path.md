# Breaking Down a Path

This is something that can be used to break down a URL or folder path.  

```
URL = ‘https://localhost/Secure/Gallery’

SlashLocation = URL.rfind(‘/‘)

WhereAreYou = URL[SlashLocation + 1]

```

  

This will return “Gallery”.