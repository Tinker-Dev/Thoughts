# AP with AdGuard DNS

Here you will find instructions to turn a Raspberry Pi in to a WiFi Extender that has ad blocking ablities.

  

## Giving Credit

The details on this page are a mashup of these two projects from PiMyLifeUp.

- [WiFi Extender](https://pimylifeup.com/raspberry-pi-wifi-extender/)
- [Adguard Home](https://pimylifeup.com/raspberry-pi-adguard-home/)

  

## For Thought:

You need to think about how this AP is going to get used to choose the right version of the Raspberry Pi OS.

  

## The Traveling AP

- If you are going to be using this as a AP for traveling, then you are going to want to have an OS with a GUI so that you can authenticate at captured portals.
- Having an AP for this can be very helpful. You will be firewalled off from the rest of the environment. Not to mention the quick connecting of personal or family devices.

  

### Single Location AP

- If this is the intent then you are fine running the lite version of the Raspberry Pi OS.

  

## What you will need:

- **Raspberry Pi**

    - We have used Zero W, Zero 2 W, and 3B+
    - Zero W has plenty of power for this

- **Pi Friendly WiFi Dongle**

    - We used the [CanaKit Dongle](https://www.canakit.com/raspberry-pi-wifi.html)

## Base Knowledge

Here is how our adapters are configured.

- Internal WiFi = wlan0
- External WiFi = wlan1

  

## Update Your system

Run the following to make sure that your system is fully ready.

```
sudo apt update && sudo apt full-upgrade -y
```

  

## Assumption

The idea is that the RPi has is already connected to the internet via wlan0. This is how you will be able to download the needed packages.

  

## Configuring Access Point and Setting Static IP

  

### Install Needed Items

We have a couple of packages that we are going to need so lets get them installed.

```
sudo apt install hostapd -y
sudo apt install iptables -y
```

  

### Start At Boot

Now that we have those installed lets set hostapd set to start at boot.

```
sudo systemctl unmask hostapd
sudo systemctl enable hostapd
```

  

### Define Static IP

Open the dhcpcd.conf to apply our settings

```
sudo nano /etc/dhcpcd.conf
```

  

Add these lines to the config. Feel free to choose your own IP scheme.

```
interface wlan1
static ip_address=10.10.10.10/24
nohook wpa_supplicant
```

  

### Forward Traffic

Open the sysctl.conf and modify it as below.

```
sudo nano /etc/sysctl.conf
```

  

Remove the Commment from Exsiting Line:

From 

```
#net.ipv4.ip_forward=1
```

  

To

```
net.ipv4.ip_forward=1
```

  

Solidify those changes.

```
sudo sh -c "echo 1 > /proc/sys/net/ipv4/ip_forward"
```

  

Set some base routing rules.

```
sudo iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE  
sudo iptables -A FORWARD -i wlan0 -o wlan1 -m state --state RELATED,ESTABLISHED -j ACCEPT  
sudo iptables -A FORWARD -i wlan1 -o wlan0 -j ACCEPT

```

  

Save the routing tables.

```
sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"

```

  

Open the rc.local so we can set some run at boot options.

```
sudo nano /etc/rc.local
```

  

Add the following line **\*\*just before the Exit line\*\***.

```
iptables-restore < /etc/iptables.ipv4.nat
```

  

  

### Define AP WiFi

Open the hostapd.conf to add some details.

```
sudo nano /etc/hostapd/hostapd.conf
```

  

  

#### Make a Choice

What type of AP do you want? An Open or Closed?

  

##### Open AP

- Use these settings as your base if you would like an Open AP. As in one that someone can join without a password.

```
interface=wlan1
ssid=PublicWifi
hw_mode=g
channel=7
auth_algs=1
wmm_enabled=0
```

  

##### Secure AP

- Use this one as your base if you want to limit who can use the AP.

  

```
interface=wlan1
ssid=NetworkName
hw_mode=g
channel=7
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=PassphrasePassphrase
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
```

  

  

Start the AP service

```
sudo service hostapd start
```

  

Check the status of the AP service

```
systemctl status hostapd
```

  

Solidify Everything

```
sudo reboot
```

  

  

## Setting up DHCP, DNS, and ad blocking.

  

### Installation

Heads Up!

- You will need to check which version of ARM code your device can run.

    - Example, a `RPi Zero W` has to use ARM6 code, where as a `RPi Zero 2 W` can use ARM7 code.
    - If you device can only use ARM6 code then you will need to change the `7` to a `6` in the next two instructions.

  

Download AdGuard Home

This application is expecting you to be in the home folder of your user. You don't need to put this anywhere special. It works just fine from there.

```
wget 'https://static.adguard.com/adguardhome/release/AdGuardHome_linux_armv7.tar.gz'
```

  

Unpack the Downloaded File

```
tar -f AdGuardHome_linux_armv7.tar.gz -x -v
```

  

Change directory to the unpacked folder

```
cd ./AdGuardHome/
```

  

Install Ad Guard Home

```
sudo ./AdGuardHome -s install
```

  

  

### The Wizard

Once the install is complete then connect to the setup web interface and go through the basic wizard setup.

  

#### Suggestion:

- Change the web admin port to anything other than 80. Just remember what you changed it to.

    - A good suggestion would be 8080.

- Leave the default port of the the DNS.

  

Once you are done with the wizard it will have you login to the Admin Interface, do that.

  

### DNS

- AdGuard is a DNS server with filtering, so just installing it took care of the DNS part of the process.

  

### DHCP Configuration

#### DHCP Settings

Once you logged into the admin interface go to `Settings -> DHCP Settings` and set the following settings.

- Select DHCP interface = `wlan1 - 10.10.10.10`
- Gateway IP = `10.10.10.10`
- Range of IP addresses = `10.10.10.100 - 10.10.10.200`
- Subnet mask = `255.255.255.0`

  

Save your configuration.

  

#### Start DHCP Server

- At the top of the screen click `Check for DHCP servers` button.

    - This will take a few seconds.
    - Ideally you get some red boxes stating that it can't find a DHCP server, that is great.

  

- Now it will let you click the `Enable DHCP Server` button, do that.

    - It should have stated that it was able to enable DHCP.

  

### All Done

At this point you are done and just need to connect to your AP.

  

Now would be a good time to check out all the features AdGuard has to offer. I would suggest going over to `Filters -> DNS Block Lists` and adding a few more.