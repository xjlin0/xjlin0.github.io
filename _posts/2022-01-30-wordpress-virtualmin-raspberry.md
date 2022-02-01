---
layout: post
title: "Wordpress on Raspberry Pi with Virtualmin"
description: "Running a Wordpress on a Raspberry Pi with admin UI for kids"
category: tech
tags: [Raspberry Pi, Virtualmin, DevOps]
---
{% include JB/setup %}
### Kids: What is a web server? <img src="/assets/imgs/incredibles_computer.jpg"  alt="Bob using a computer" width="40%"/>

Last weekend my nephew came over with computer parts and I helped him to assemble his first PC. He's not sure what to do with his old Raspberry Pi.  That's a great chance to explain the concept of web servers and clients.  Compared to k8 & containers, physical things are generally easier to understand for early teenagers.

In the end I setup a tiny Wordpress website on the Raspberry Pi with Virtualmin, a GUI for server administration.  Out of my surprise, the Worpress site's not slow and very smooth for a single visitor, after the site administration done in Virtualmin GUI.  Generally the server load is between 0.5~1.0 and consumes no more than 3W. There are [tons of hosting options](https://smartblogger.com/free-wordpress-hosting/) but such settings might be useful for demo process later.

<hr/>
| Item        | Version     |
| ----------- | ----------- |
| Server      | Raspberry Pi 3B+, with 64G microSD |
| OS          | Raspberry Pi OS (Debian 10 **Buster** Linux kernel 5.10) |
| Virtualmin  | 6.17 (with Webmin 1.984) |
{:style="width:100%;"}

<hr/>

1\. [OS installation] Because the latest Raspberry Pi OS moved to Debian 11 **Bullseye** after October 2021, please use [Raspberry Pi Imager](https://www.raspberrypi.com/software/) to write [Raspberry Pi OS (Legacy)](https://www.raspberrypi.com/software/operating-systems/#raspberry-pi-os-legacy) to the microSD card, since currently Virtualmin installation script only support Debian 10 **Buster**.  You may choose to install without Desktop software to make the server cleaner.

2\. [Setup swap] Although Virtualmin installation script provide an option to change swap size, I prefer to increase to 1GB swap in Raspberry way:

```sh
sudo dphys-swapfile swapoff
sudo nano /etc/dphys-swapfile
#Find the line of CONF_SWAPSIZE=100 and change to 1024 (1GB), save and exit
sudo dphys-swapfile setup
sudo dphys-swapfile swapon
```

3\. [Change root password] Please DO change default "pi" user's password since the site maybe public.

```sh
sudo su
passwd
exit
``` 

4\. [Get a FDQN domain name] This will be needed in the next steps.  Since this is an in-home setting, I changed the router's setting to expose the Raspberry Pi to public by add a Game or NAT mapping on forwarding port 80/443.  If you don't have NoIP.com or DynDNS.org account, nslookup your public ip may get you a temporary FDQN from ISP.

```sh
sudo apt-get -y install dnsutils
nslookup <your public IP>
# find the FDQN domain name that ISP assigned
```

5\. [[Install Virtualmin on Raspberry Pi](https://forums.raspberrypi.com/viewtopic.php?t=254307#p1690845)] Since Raspberry Pi OS is not officially supported by Virtualmin, we need to force the installation script recognize the system as Debian.  The installation may take quite some time.

```sh
wget http://software.virtualmin.com/gpl/scripts/install.sh
nano install.sh
#Find the text "get_distro", around line # 600
#Add a new line of os_type="debian" immediately after the line of get_distro, save and exit
sudo sh ./install.sh  # it will ask for FDQN domain name
```

6\. [Virtualmin post installation] After Virtualmin installation from shell it will ask you to visit Virtualmin page on your Raspberry Pi to finish post-installation settings. Since Virtualmin port is default 10000, you may need to visit something like https://`your Raspberry Pi local ip`:10000, and force accept self signed certificate by typing this in developer console. Then login with the root password in step 3.
```
sendCommand(SecurityInterstitialCommandId.CMD_PROCEED)
```
Please also add a Virtual server with a new user (owner). And grant the new user more user permission by Virtualmin -> Administration Options -> Edit Owner Limit ->  Can install scripts/Can manage databases/Can schedule backups/Can make backups.

7\. (optional)[More PHP versions] If newer PHP versions are preferred instead of PHP 7.3 default in Debian 10 Buster, [setup repository for PHP now](https://www.itzgeek.com/how-tos/linux/debian/how-to-install-php-7-3-7-2-7-1-on-debian-10-debian-9-debian-8.html), and [you can add multiple PHP versions later](https://www.virtualmin.com/documentation/web/multiplephp/#Installing_PHP_56_andor_74_andor_80_on_Debian_910) but please consider the [PHP version supported in Wordpress](https://make.wordpress.org/core/handbook/references/php-compatibility-and-wordpress-versions/) as well as its plugins.

```sh
sudo apt install -y curl wget gnupg2 ca-certificates lsb-release apt-transport-https
wget https://packages.sury.org/php/apt.gpg
sudo apt-key add apt.gpg
echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/php7.list
sudo apt update
```

If multiple PHP versions are installed, you may want to assign a default PHP version by visiting System Settings -> Server Templates -> Default -> PHP Options.

8\. [Setup Wordpress] Logout Virtualmin page and login with the new user.  Visit Virtualmin -> Install Scripts, select Wordpress, install and finish the Wordpress.  You may setup automatic SSL renewal for the new site by visiting Virtualmin -> Server Configuration -> SSL Certificate -> Let's Encrypt. enjoy!

