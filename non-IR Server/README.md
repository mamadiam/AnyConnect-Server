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
