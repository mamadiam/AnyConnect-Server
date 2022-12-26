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
