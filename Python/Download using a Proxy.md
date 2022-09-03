# Download using a Proxy

This is the base code needed to download a file from behind a proxy. If you don't use something like this you will most likely start getting SSL errors.  

  

```
################################# Load Modules #################################

import requests


############################# Configuration Options ############################

# Choosing the proxy to use. Otherwise we can get outside the firewall.
conf_proxy_server = "SERVER AND PORT"

# Where are you downloading the file from?
conf_source_url = ''

# The path of where are you want to send the file? (Don't include the file name.)
conf_destination_path = r''

# What do you want to call the file.
conf_file_name = ''

############################# Functions #############################

def download_file():

        # Defining the whole destination path.
        file_path = conf_destination_path + "\\" + conf_file_name

        # Defining the Proxy connection.
        proxies = {'http': conf_proxy_server,
                   'https': conf_proxy_server}

        # Requesting the data from the internet.
        request_binary = requests.get(conf_source_url, proxies=proxies)
        content = request_binary.content

        # Saving the data to file.
        output_file = open(conf_destination_path, 'wb')
        output_file.write(content)
        output_file.close()


```