# Hello World Apache On Debian
[![OS badge](https://img.shields.io/badge/OS-Debian-red.svg)](https://www.debian.org)
[![Server badge](https://img.shields.io/badge/Server-Apache-blue.svg)](https://httpd.apache.org)
[![Format badge](https://img.shields.io/badge/Format-HTML-green.svg)](https://lyty.dev/html/index.html)

## Objectif 

Create page HTML for **hello-world.hephaiscode.com** with Apache and HTML File

## You need

- Server on Debian (linux distribution) with root access
- DN (Domain Name) point on Server IP (example hello-world.hephaiscode.com on 51.83.45.10)
- Terminal/console to enter instruction

## Parameters

We use :
 - hello-world.hephaiscode.com for domain name of our web page
 - 51.83.45.10 the IP address of our server computer
 - Create an user hephaistos with password 'Gkh23hglxVd47ShG43jh3h' (change the password!)
 
 
## Connect to server 

Open terminal or console and go to admin your server.

```
ssh -l root 51.83.45.10 
```

And enter your root password 

## Update your server

Always to be update.

```
apt-get update
apt-get -y upgrade
apt-get -y dist-upgrade
```

## Install Apache

Install Apache as WebServer to communicate with your server at **hello-world.hephaiscode.com **

```
apt-get -y install apache2
apt-get -y install apache2-doc
apt-get -y install apache2-suexec-custom
apt-get -y install logrotate
systemctl restart apache2
```
 
 ## Define our parameters
 
 ```
 MYUSER=hephaistos
 MYPASSWORD=Gkh23hglxVd47ShG43jh3h
 MYDOMAINNAME=hello-world.hephaiscode.com
 MYEMAIL=hello-world@hephaiscode.com
 MYWEBFOLDER=WebSite
 ```
 
 ## Create user
 
 ```
useradd --shell /bin/false ${MYUSER}
echo ${MYUSER}:${MYPASSWORD} | chpasswd
mkdir /home/${MYUSER}
chown root /home/${MYUSER}
chmod go-w /home/${MYUSER}
```

Add directories

```
mkdir /home/${MYUSER}/
mkdir /home/${MYUSER}/ssl
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout /home/${MYUSER}/ssl/${MYDOMAINNAME}.key -out /home/${MYUSER}/ssl/${MYUSER}.crt -subj \"/C=FR/ST=LILLE/L=LILLE/O=Global Security/OU=IT Department/CN=${MYUSER}\"
chown -R ${MYUSER}:www-data /home/${MYUSER}/ssl",
chmod -R 750 /home/${MYUSER}/ssl",

mkdir /home/${MYUSER}/${MYWEBFOLDER}_SSL
echo "<?php echo phpinfo();?>" >> /home/${MYUSER}/${MYWEBFOLDER}_SSL/phpinfo.php
echo "Hello World! You are secure!" >> /home/${MYUSER}/${MYWEBFOLDER}_SSL/index.html
chown -R ${MYUSER}:www-data /home/${MYUSER}/${MYWEBFOLDER} _SSL
chmod -R 750 /home/${MYUSER}/${MYWEBFOLDER}_SSL

mkdir /home/${MYUSER}/${MYWEBFOLDER}_NOSSL
echo "<?php echo phpinfo();?>" >> /home/${MYUSER}/${MYWEBFOLDER}_NOSSL/phpinfo.php
echo "Hello World! You are NOT secure! Please use SSL connexion!" >> /home/${MYUSER}/${MYWEBFOLDER}_NOSSL/index.html
chown -R ${MYUSER}:www-data /home/${MYUSER}/${MYWEBFOLDER}_NOSSL
chmod -R 750 /home/${MYUSER}/${MYWEBFOLDER}_NOSSL
```

## Install Domain Name

Create the host parameters for Apache and our domains **hello-world.hephaiscode.com**

```
MYAPACHECONFNOSSL=/etc/apache2/sites-available/${MYDOMAINNAME}.conf
rm ${MYAPACHECONFNOSSL}
echo "<VirtualHost *:80>" >> ${MYAPACHECONFNOSSL}
echo "Protocols h2 http/1.1" >> ${MYAPACHECONFNOSSL}
echo "ServerAdmin ${MYEMAIL}" >> ${MYAPACHECONFNOSSL}
echo "ServerName ${MYDOMAINNAME}" >> ${MYAPACHECONFNOSSL}
echo "ServerAlias ${MYDOMAINNAME}" >> ${MYAPACHECONFNOSSL}
echo "DocumentRoot /home/${MYUSER}/${}_NOSSL" >> ${MYAPACHECONFNOSSL}
echo "<Directory />" >> ${MYAPACHECONFNOSSL}
echo "AllowOverride All" >> ${MYAPACHECONFNOSSL}
echo "</Directory>" >> ${MYAPACHECONFNOSSL}
echo "<Directory /home/helloworld>" >> ${MYAPACHECONFNOSSL}
echo "Options Indexes FollowSymLinks MultiViews" >> ${MYAPACHECONFNOSSL}
echo "AllowOverride all" >> ${MYAPACHECONFNOSSL}
echo "Require all granted" >> ${MYAPACHECONFNOSSL}
echo "</Directory>" >> ${MYAPACHECONFNOSSL}
echo "LogLevel error" >> ${MYAPACHECONFNOSSL}
echo "ErrorLog /var/log/apache2/helloworld-error.log" >> ${MYAPACHECONFNOSSL}
echo "CustomLog /var/log/apache2/helloworld-ssl-access.log combined env=NoLog" >> ${MYAPACHECONFNOSSL}
echo "</VirtualHost>" >> ${MYAPACHECONFNOSSL}
a2ensite ${MYAPACHECONFNOSSL}
systemctl restart apache2
```

## File Script
Write root's file in HTML. The name is index.html
```
rm /home/helloworld/index.html
echo '<html><body>Hello World!</body></html>' >> /home/helloworld/index.html
```

## Hello World Success
Open browser and go to page http://hello-world.hephaiscode.com 

![Success](https://img.shields.io/badge/Hello%20World-OK-Green.svg)
