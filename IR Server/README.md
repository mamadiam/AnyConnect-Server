## on Your IR Server</br>

### 1. Connect Server to Cloudflare CDN or ArvanCloud.</br>
 (whichever you choose), I'll go by with Cloudflare</br></br>
&nbsp;&nbsp; 1-1. Login into cloudflare Account</br>
&nbsp;&nbsp; 1-2. Click + to add the domain you purchase from [Freenom](https://www.freenom.com/en/index.html?lang=en) to your account</br>
&nbsp;&nbsp; 1-3. Set cloudflare NS to your domain</br>
&nbsp;&nbsp; 1-4. In DNS > DNS Records</br>
&nbsp;&nbsp; 1-5. Create new A Record, Set Name to your Subdomain and IPv4 address to your IR Server</br>
&nbsp;&nbsp; 1-6. Turn off Proxy </br></br>

#### Now let's set up the server. </br>
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
### 2. Install requirements </br>
#### 2-1. update & upgrade the packages </br>
```shell script
sudo apt update && apt upgrade -y
``` 
#### 2-2. Install curl and socat </br>
```shell script
sudo apt install curl socat -y
``` 
#### 2-3. Install iptables </br>
```shell script
sudo apt-get install iptables
``` 
Note: I've try `iptables-persistent`, It doesn't work with the most of the datacenters and they drop the connections quickly.</br>
</br>
### 3. Get SSL </br>
#### 3-1. Install acme script </br>
```shell script
curl https://get.acme.sh | sh
``` 
#### 3-2. Set the default provider to Let’s Encrypt </br>
Note: you can use `cerbot` or any other services you want.
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
</br>

### 4. Set iptables values </br>

#### 4-1. Run this command all at once </br>
Note: First copy it into an editor and change the `IP Server` and `non-IR Server`.</br>

```shell script
sysctl net.ipv4.ip_forward=1
iptables -t nat -A PREROUTING -p tcp --dport 22 -j DNAT --to-destination IP Server
iptables -t nat -A PREROUTING -j DNAT --to-destination non-IR Server
iptables -t nat -A POSTROUTING -j MASQUERADE
```

</br>
If you want to make your changes permenant incase of reboot, do the following step</br>

#### 4-2. Open rc.local </br>

```shell script
sudo nano /etc/rc.local
```

</br>
Now paste the rules we declare before
</br>

```shell script
#! /bin/bash
sysctl net.ipv4.ip_forward=1
iptables -t nat -A PREROUTING -p tcp --dport 22 -j DNAT --to-destination IP Server
iptables -t nat -A PREROUTING -j DNAT --to-destination non-IR Server
iptables -t nat -A POSTROUTING -j MASQUERADE
exit 0
```

</br>
#### 4-3. Give it executable allowance. </br>

```shell script
sudo chmod +x /etc/rc.local
```

</br>
Now, at this point you are good to go and can connect to your `ocserv` server but there is a tiny step you could take for more security.

### 5. Transfer SSL to ocserv server (Optional)</br>
I'm assuming you used `Let's Encrypt` for generating ssl.

#### 5-1. Navigate to this directoy </br>
```shell script
cd /root/.acme.sh/your-domain
```

#### 5-2. Copy both `.cer` and `key` files </br>

#### 5-3. Now login to your non-IR server </br>

#### 5-4. Navigate to this directoy </br>

```shell script
cd /etc/ocserv/ssl/
```

#### 5-5. Paste both files into this directory </br>

#### 5-6. Open the `ocserv.conf` file </br>

```shell script
nano /etc/ocserv/ocserv.conf
```

#### 5-7. Change the server cert and key in the `ocserv.conf` file </br>

```shell script
server-cert = /etc/ocserv/ssl/server-cert.pem
server-key = /etc/ocserv/ssl/server-key.pem
```

to

```shell script
server-cert = /etc/ocserv/ssl/your-domain.cer
server-key = /etc/ocserv/ssl/your-domain.key
```
