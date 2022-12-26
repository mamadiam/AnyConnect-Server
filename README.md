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

## on Your IR Server</br>

If you have any problems on the connections and couldn't get the packages, there are two ways you can solve this</br>
1. Use Shekan DNS  </br>
#### Edit the resolv.conf  </br>
```shell script
sudo nano /etc/resolv.conf
``` 
and set the followings.
```shell script
nameserver 178.22.122.100
nameserver 185.51.200.2
#nameserver 127.0.0.53
#options edns0
``` 
2. Use ArvanCloud mirror servers.  </br>
#### Edit the resource.list  </br>
```shell script
sudo nano /etc/apt/sources.list
``` 
and set the url below for servers
```shell script
deb http://mirror.arvancloud.ir/ubuntu bionic universe
``` 
Personnally I changed the DNS and it works like a charm.</br></br>
Now that you are up and running, let's get started.</br></br>
### 1. Install iptables </br>
#### 1-1. update & upgrade the packages </br>
```shell script
sudo apt update && apt upgrade -y
``` 
#### 1-2. Install curl and socat </br>
```shell script
sudo apt install curl socat -y
``` 
#### 1-3. Install iptables </br>
```shell script
sudo apt-get install iptables
``` 
Note: I've try `iptables-persistent`, It doesn't work with the most of the datacenters and they drop the connections quickly.</br>

#### 1-4. Run this command all at once </br>
Note: First copy it into an editor and change the `IP Server` and `non-IR Server`.</br>
```shell script
sysctl net.ipv4.ip_forward=1
iptables -t nat -A PREROUTING -p tcp --dport 22 -j DNAT --to-destination "IP Server"
iptables -t nat -A PREROUTING -j DNAT --to-destination "non-IR Server"
iptables -t nat -A POSTROUTING -j MASQUERADE
```
If you want to make your changes permenant incase of reboot, do the following step
#### 1-5. Open rc.local </br>
```shell script
sudo nano /etc/rc.local
```
Now paste the rules we declare before</br>
```shell script
sysctl net.ipv4.ip_forward=1
iptables -t nat -A PREROUTING -p tcp --dport 22 -j DNAT --to-destination "IP Server"
iptables -t nat -A PREROUTING -j DNAT --to-destination "non-IR Server"
iptables -t nat -A POSTROUTING -j MASQUERADE
```

### 2. Connect Server to Cloudflare CDN or ArvanCloud (whichever you choose), I'll go by with Cloudflare</br></br>
&nbsp;&nbsp; 2-1. Login into cloudflare Account</br>
&nbsp;&nbsp; 2-2. Add your domain you purchase from [Freenom](https://www.freenom.com/en/index.html?lang=en)</br>
&nbsp;&nbsp; 2-3. Click + Add new domains</br>
&nbsp;&nbsp; 2-4. Add new Domain and set cloudflare NS to your domain</br>
&nbsp;&nbsp; 2-5. In DNS > DNS Records</br>
&nbsp;&nbsp; 2-6. Create new A Record, Set Name to your Subdomain and IPv4 address to your IR Server</br>
&nbsp;&nbsp; 2-7. Turn off Proxy </br></br>
### 3. Get SSL

#### 3-1. Install acme script </br>
```shell script
curl https://get.acme.sh | sh
``` 
#### 3-2. Set the default provider to Let’s Encrypt </br>
```shell script
~/.acme.sh/acme.sh --set-default-ca --server letsencrypt
``` 
#### 3-3. Register your account </br>
Note: use your actual email address
```shell script
~/.acme.sh/acme.sh --register-account -m youremail@exmple.com
``` 
#### 3-4. Set your Sub-Domain </br>
Note: replace host.mydomain.com by your actual host name
```shell script
~/.acme.sh/acme.sh --issue -d host.mydomain.com --standalone
``` 
Now lets move your certificate and key to somewhere else, we gonna need them later. shall we?</br>
#### 3-4. Set your Sub-Domain </br>
Note: replace host.mydomain.com by your actual host name
```shell script
~/.acme.sh/acme.sh --installcert -d host.mydomain.com --key-file /root/private.key --fullchain-file /root/cert.crt
``` 

