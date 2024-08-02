### FileMakerServer-LetsEncrypt-Ubuntu-Nginx-Certbot
Bash scripts for fetching and renewing Let's Encrypt (certbot) certificates for FileMaker Server running on Linux (Ubuntu) using the default system timers installed by snap when you install certbot and hooks to restart FMS only when the certificate is renewed and at a certain time of day to avoid disruption of access to users.

### Reference Article
https://mhtawfiq.github.io/deploying-filemaker-server-on-ubuntu-linux-with-lets-encrypt-ssl/

#### Initial Setup Instructions:
##### 1. Setup Ubuntu + install FMS (as of now, its 19.6.3)

##### 2. Install at
```
apt install at
```

##### 2. Install certbot
```
sudo snap install core; sudo snap refresh core
sudo apt-get remove certbot
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
##### 3. Logged in as root:
```
cd ~/
mkdir fms-ssl
cd fms-ssl/
```
##### 4. Download & make executable
```
wget https://raw.githubusercontent.com/mhtawfiq/FileMaker-LetsEncrypt-Ubuntu-Nginx/main/fms-ssl/get-ssl.sh
wget https://raw.githubusercontent.com/mhtawfiq/FileMaker-LetsEncrypt-Ubuntu-Nginx/main/fms-ssl/renew-cert.sh
wget https://raw.githubusercontent.com/mhtawfiq/FileMaker-LetsEncrypt-Ubuntu-Nginx/main/fms-ssl/renew-cert-at.sh
chmod +x get-ssl.sh renew-cert.sh renew-cert-at.sh
``` 
##### 5. Edit content of scripts
```
nano get-ssl.sh
```
Set domain, fms admin username and password and email
```
nano renew-cert.sh
```
Set domain, fms admin username and password
```
nano renew-cert-at.sh
```
Set the time of day to schedule FileMaker Server restart when the certificate is renewed by certbot systemctl timer
##### 6. Generate the ssl certificate for the first time
```
sudo ./get-ssl.sh
```
#### Renewal Setup Instructions:
The systemctl timer installed by certbot by default checks twice a day at a random time if the certificate needs renewal, only if the certificate is renewed will it run pre and post hooks for additional processing.

Create a symbolic link to run renew-cert-at.sh as a post hook:
```
ln -s ~/fms-ssl/renew-cert-at.sh /etc/letsencrypt/renewal-hooks/post/
```
This insures that once the certificate is renewed by the systemctl timer, FileMaker Server will only restart at the time scheduled in renew-cert-at.sh to avoid disruption of access to users at an undesired time.

Forked from https://github.com/jon91/FileMaker-LetsEncrypt-CentOS-7
