# Digital Geocache

## Setup Web Server

Update the Repository First  

```
sudo apt-get update 
sudo apt-get upgrade

```

  

We need to remove Apache, if it is installed, as there are few operating systems the have it baked in.  

```
sudo apt-get remove apache2

```

  

  

### Install NGINX

Installing the Webserver  

```
sudo apt-get install nginx -y

```

  

Start the Server  

```
sudo systemctl start nginx

```

  

### Install PHP

- NGINX won’t be automatically set up for use with PHP. We instead must make changes to its configuration files to get it to load in.
- We will also have to utilize PHP-FPM and not standard PHP due to the way NGINX works.

  

Install  

```
sudo apt-get install php7.3-fpm php7.3-mbstring php7.3-mysql php7.3-curl php7.3-gd php7.3-curl php7.3-zip php7.3-xml -y

```

  

Configure NGINX to use PHP  

Here we need to add `index.php` to the index line, and this tells NGINX to recognize the `index.php` file as a possible index, adding it first in the list means it will be selected over an `index.html` file.  

```
sudo nano /etc/nginx/sites-enabled/default

```

  

Find:  

```
index index.html index.htm;

```

  

Replace With:  

```
index index.php index.html index.htm;

```

  

Find:  

```
#location ~ \.php$ { 
	# include snippets/fastcgi-php.conf; 
	# 
	# # With php5-cgi alone: 
	# fastcgi_pass 127.0.0.1:9000; 
	# # With php5-fpm: 
	# fastcgi_pass unix:/var/run/php5-fpm.sock; 
	#}

```

  

Replace With:  

```
location ~ \.php$ {
	include snippets/fastcgi-php.conf;
	fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
	}

```

  

Reload NGINX  

```
sudo systemctl reload nginx

```

  

  

## Setup AP

### Install Hostapd

Install  

```
sudo apt install hostapd

```

  

Enable Automatic Startup  

```
sudo systemctl unmask hostapd
sudo systemctl enable hostapd

```

  

### Install DNSMASQ

Install  

```
sudo apt install dnsmasq -y

```

  

Add IP Table Rules  

```
sudo DEBIAN_FRONTEND=noninteractive apt install -y netfilter-persistent iptables-persistent

```

  

### Static IP

Config  

```
sudo nano /etc/dhcpcd.conf

```

  

Add these lines at the end of the file:  

```
interface wlan0
static ip_address=10.10.10.10/24
nohook wpa_supplicant

```

  

### Enable Routing

Create/Open Config  

```
sudo nano /etc/sysctl.d/routed-ap.conf

```

  

Add Lines  

```
net.ipv4.ip_forward=1

```

  

SPECIAL - Ethernet Firewall Rule  

- You only need to run this if your Pi has Ethernet too.

```
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

```

  

Set Rules to Load at Startup  

```
sudo netfilter-persistent save

```

  

### Configure the DHCP and DNS

Create the a Config File:  

```
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig

```

  

Edit the new config file:  

```
sudo nano /etc/dnsmasq.conf

```

  

Add these lines:  

  

Version 1:  

```
interface=wlan0
dhcp-range=10.10.10.11,10.10.10.20,255.255.255.0,24h
domain=wlan
address=/gw.wlan/10.10.10.10

```

  

Version 2:  

```
interface=wlan0
dhcp-range=10.10.10.11,10.10.10.20,255.255.255.0,24h
domain=wlan
address=/#/10.10.10.10
no-hosts
addn-hosts=/etc/dnsmasq.hosts

```

  

If you went with Version 2 from above then you will need to do this part as well.  

```
sudo nano /etc/dnsmasq.hosts

```

  

Add this:

```
10.10.10.10 geo.chat

```

  

## Create a Network Name and Password

Open  

```
sudo nano /etc/hostapd/hostapd.conf

```

  

Add these lines:

```
interface=wlan0
ssid=NetworkName
hw_mode=g
channel=7
auth_algs=1
wmm_enabled=0

```

  

  

## Make it Stick

Reboot

```
sudo reboot

```