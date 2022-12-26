# AnyConnect-Server
Installation Script for Cisco AnyConnect

Deploy a Cisco AnyConnect compactable ocserv server on Linux (Tested on Ubuntu)</br>
This project is heavely inspired from the github user <a href='https://github.com/sfc9982'> sfc9982 </a> project. </br></br>
For this you need two servers, one in Iran and the other outside of Iran.</br>
you can google it or using websites like [Sindad](https://sindad.com/) or [Mihan Webhost](https://mihanwebhost.com/) or any other services you like.</br>
Note: You don't need anything fancy, even the most basic and simple plans of the VPS's will do the magic.</br>
For the foreign server I'd recommend [Digital Ocean](https://www.digitalocean.com/) or something that will give you VP's hourly.
<hr>
Your server inside Iran will act as a router. It's only job is to route traffic from within the country to the main server (non-IR) which hosts the actual ocserv. </br>
All commands are base on debian-based linux distros. e.g. Ubuntu.</br></br>
Share it with your loved ones</br></br>
<bold>#MahsaAmini</bold></br>
<bold>#womanlifefreedom</bold></br></br>
Let's get started.</br></br>

## 0. Requirements</br>

&nbsp;&nbsp; 0-1. Valid domain name, Recommended [freenom](https://www.freenom.com/en/index.html?lang=en) </br>
&nbsp;&nbsp; 0-2. [ArvanCloud](https://www.arvancloud.ir/) or [Cloudflare](https://www.cloudflare.com/)</br>
&nbsp;&nbsp; 0-3. Two VP's (one in IR, other one outside of IR) </br>
&nbsp;&nbsp; 0-4. SSL Certificate</br>

## 1. on Your non-IR Server</br>

#### 1-1. update & upgrade the packages </br>
```shell script
apt update && apt upgrade -y
``` 
#### 1-2. Install wget (if it's not already installed on the machine) </br>
```shell script
apt install wget
``` 
#### 1-3. Run this command </br>
```shell script
wget -N --no-check-certificate https://raw.githubusercontent.com/sfc9982/AnyConnect-Server/main/ocserv-en.sh
```
#### 1-4. Allow it to execute </br>
```shell script
chmod +x ocserv-en.sh
```
#### 1-5. Now run the script </br>
```shell script
bash ocserv-en.sh
```
Note: for the open connect configuration go to the `non-IR Server` directory, checkout the `ocserv.conf` and set it as I did. it works better.</br>
#### 1-6. Edit the ocserv.conf file </br>
```shell script
nano /etc/ocserv/ocserv.conf
```

#### Install BBR only if you experienced slow connection </br>
```shell script
wget -N --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && bash bbr.sh
```
<hr>
That's it for your non-IR Server, now let's setup you IR Server.</br></br>

