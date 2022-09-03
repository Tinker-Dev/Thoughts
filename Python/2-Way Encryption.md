# 2-Way Encryption

From time time there is a need to record a password so that you can use it to access a third party item. Not everything in the world has an API that only uses a token. Here is the code that will allow you to gather a password an then hash it in a way that you can recall the hash and use the password. Note: This is the kind of code that keeps honest people honest. Anyone that wants to figure out what the password is will be able to do so, with just a little work.  

  

## Requirement:

```
python -m pip install cryptography

```

  

## Step 1 - Generate the Hash Key:

```
from cryptography.fernet import Fernet
key = Fernet.generate_key()
with open('key.dll', 'wb') as file_object:  file_object.write(key)

input('Press Enter to continue...')

```

  

## Step 2 - Encrypt the Password:

```
from cryptography.fernet import Fernet
with open('key.dll', 'rb') as file_object:
    for line in file_object:
        key = line
cipher_suite = Fernet(key)
ciphered_text = cipher_suite.encrypt(b'SuperSecretpassword')
with open('encryptedpass.dll', 'wb') as file_object:  file_object.write(ciphered_text)

input('Press Enter to Continue:')

```

  

## Step 3 - Decrypt the Password for Use:

```
from cryptography.fernet import Fernet
with open('key.dll', 'rb') as file_object:
    for line in file_object:
        key = line
cipher_suite = Fernet(key)
with open('encryptedpass.dll', 'rb') as file_object:
    for line in file_object:
        encryptedpwd = line
uncipher_text = (cipher_suite.decrypt(encryptedpwd))
plain_text_encryptedpassword = bytes(uncipher_text).decode("utf-8") #convert to string
print(plain_text_encryptedpassword)

input('Press Enter to Continue:')

```