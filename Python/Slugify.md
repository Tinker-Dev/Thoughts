# Slugify

You may ask yourself what “Slugify” is. Well that is the process of cleaning up text so that it can’t cause issues when being used in code. It is the start of a sanitation process.  

  

```
# Importing the Regular Expressions module. (Native)
# "re" is used for string matching operations.
import re

# Defined a process to used when wanting to normalize string data.
def slugify(text):
    # Step 1 - Removes special characters
    #          Removes spaces before and after string
    #          Converts the string to lowercase
    text = re.sub('[^\w\s-]', '', text).strip().lower()

    # Step 2 - Changes spaces and hyphens to underscores.    
    text = re.sub(r'[- ]+', '_', text)
    
    # Step 3 - Provide the modified text out to the requesting process.
    return (text)

cleanedtext = slugify('The quick brown fox jumped over the lazy dog!')

# Output our cleaned text.
print(cleanedtext)

```