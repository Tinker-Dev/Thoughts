# Tips and Tricks

Little tips and tricks for when working with a Raspberry Pi running Debian.  

  

## Running Headless

You don't always need a GUI so here are a few things that you need to do if you are going to run headless. This only works if you are running a version of Raspberry OS.

- Once you have your SD Card imaged with the version can be mounted and it will show a "boot" drive. These steps are take against that boot drive.

  

### Enable SSH

Simply create a file called `ssh` with no extension or content and place it on the boot drive. This file will enable ssh on the first boot after the file is added. Not that the file is deleted once SSH is enabled  

  

### Wireless Settings

#### With access to more than the Boot drive:

- If the PI needs to connect to WiFi right away so that you can SSH in then you will need to set that up before boot, if you are running headless.
- Edit the `wpa_supplicant.conf`.

```
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf

```

  

Then add this detail to the file. You will need to set some values for your network.  

```
network={
	ssid="testing"
    psk="testingPassword"
    	}

```

  

#### Without access to more than the Boot drive:

- Create a file called `wpa_supplicant.conf` on the Boot drive.
    

    - It is suggested that you use Notepad++ as you will need to make the distinction of "UNIX" file format.

- Add the following details to the file.
    

```
country=us
update_config=1
ctrl_interface=/var/run/wpa_supplicant

network={
 scan_ssid=1
 ssid="MyNetworkSSID"
 psk="Pa55w0rd1234"
}

```

  

  

## Change Config from Command Line

From time to time there are things that you need to do to the Raspberry Pi config file. Now if you are running the GUI that is very easy to get to and make changes, so here is the command to bring it up for when you are running command line only.

  

Actually you can find the toggle for the GUI vs Command Line Only here.

```
sudo raspi-config

```