## on Your IR Server</br>

#### 1-1. update & upgrade the packages </br>
```shell script
sudo apt update && apt upgrade -y
``` 
#### 1-2. Install iptables </br>
```shell script
sudo apt-get install iptables
``` 
Note: I've try `iptables-persistent`, It doesn't work with the most of the datacenters and they drop the connections quickly.</br>

#### 1-3. Run this command all at once </br>
Note: First copy it into an editor and change the `IP Server` and `non-IR Server`.</br>
```shell script
sysctl net.ipv4.ip_forward=1
iptables -t nat -A PREROUTING -p tcp --dport 22 -j DNAT --to-destination "IP Server"
iptables -t nat -A PREROUTING -j DNAT --to-destination "non-IR Server"
iptables -t nat -A POSTROUTING -j MASQUERADE
```
If you want to make your changes permenant incase of reboot, do the following step
#### 1-4. Open rc.local </br>
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
