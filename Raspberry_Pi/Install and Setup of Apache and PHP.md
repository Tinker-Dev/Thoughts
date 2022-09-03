# Install and Setup of Apache and PHP

This version is here because there are a few changes that need to be made for the Raspberry Pi version of the OS.  

  

## Apache Install

Install Apache  

```
sudo apt install apache2 -y

```

  

Restart Apache  

```
sudo service apache2 restart

```

  

  

## PHP Install

PHP is ever evolving so when you install it, you should make sure that you are installing the latest version. In

our case the Raspberry Pi repo is a little slow on the uptake, however there is a highly trusted group that helps us get it sooner. 

  

Add the GPG key of of the Sury Repo.

```
curl https://packages.sury.org/php/apt.gpg | sudo tee /usr/share/keyrings/suryphp-archive-keyring.gpg >/dev/null

```

  

Create a source file to point to the repo.

```
echo "deb [signed-by=/usr/share/keyrings/suryphp-archive-keyring.gpg] https://packages.sury.org/php/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/sury-php.list

```

  

Let Aptitude know there is a new repo to work with.

```
sudo apt update

```

  

Install PHP Version 8.0  

```
sudo apt -y install php8.1 php-mbstring php-mysql php-curl php-gd php-zip php-xml php-sqlite3 php-imagick php-intl php-tidy

```

  

Set User and Folder Permissions  

```
sudo usermod -a -G www-data pi
sudo chown -R -f www-data:www-data /var/www/html

```

  

Reboot the Machine  

  

## Keep the folder tree from being viewable

Keep the folder tree from being viewable by making the noted changes to the following file.  

```
sudo nano /etc/apache2/apache2.conf

```

  

Before:  

```
<Directory /var/www/>
Options Indexes FollowSymLinks
AllowOverride None
Require all granted
</Directory>

```

  

After:  

```
<Directory /var/www/>
Options FollowSymLinks
AllowOverride All
Require all granted
DirectoryIndex index.html index.php
</Directory>

```

  

Restart Apache  

```
sudo service apache2 restart

```

  

### Update PHP Settings

```
sudo nano /etc/php/8.0/apache2/php.ini

```

  

Set the Following Settings:  

```
max_execution_time = 60
memory_limit = 256M
post_max_size = 128M
upload_max_filesize = 100M

```

  

Activate the Following Extensions:  

```
extension=curl
extension=gd2
extension=intl
extension=mbstring
extension=exif
extension=tidy
extension=imagick.so

```

  

Restart Apache  

```
sudo service apache2 restart

```

  

  

## .htaccess

If you want to make it so that a folder and all the children are indexed and viewable then you can add the following code to the .htaccess file.  

```
Options +Indexes

```