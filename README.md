# "Hello World!" with Apache PHP and Cerbot on Debian 10

[![OS badge](https://img.shields.io/badge/OS-Debian-red.svg)](https://www.debian.org)
[![Server badge](https://img.shields.io/badge/Server-Apache-blue.svg)](https://httpd.apache.org)
[![Format badge](https://img.shields.io/badge/Format-HTML-green.svg)](https://lyty.dev/html/index.html)

## Objectif 

Create Web Page for **hello-world.hephaiscode.com** with Apache and HTML File. Access by URL  https://hello-world.hephaiscode.com .

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
ssh-keygen -R 51.83.45.10
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

Install Apache as WebServer to communicate with your server at **hello-world.hephaiscode.com**

```
apt-get -y install apache2
apt-get -y install apache2-doc
apt-get -y install apache2-suexec-custom
apt-get -y install logrotate

systemctl restart apache2

```

Active Apache modules

```
a2enmod userdir
a2enmod suexec
a2enmod http2

systemctl restart apache2

```

Configure Apache

```
sed -i 's/\/var\/www/\/home/g' /etc/apache2/suexec/www-data
sed -i 's/^.*ServerSignature .*$//g' /etc/apache2/apache2.conf
sed -i '$ a ServerSignature Off' /etc/apache2/apache2.conf
sed -i 's/^.*SSLProtocol .*$//g' /etc/apache2/apache2.conf
sed -i '$ a SSLProtocol all -SSLv2 -SSLv3 -TLSv1 -TLSv1.1' /etc/apache2/apache2.conf

systemctl restart apache2

```

Configure Default Apache by rewrite virtualhost

```
chgrp -R www-data /var/www/html/
chmod 750 /var/www/html/

rm /var/www/html/index.html
echo "<html><body>Are you lost? Ok, I'll help you, you're in front of a screen...</body></html>" > /var/www/html/index.html

rm /etc/apache2/sites-available/000-default.conf
echo '<VirtualHost *:80>' >> /etc/apache2/sites-available/000-default.conf
echo 'ServerAdmin webmaster@localhost' >> /etc/apache2/sites-available/000-default.conf
echo 'DocumentRoot /var/www/html' >> /etc/apache2/sites-available/000-default.conf
echo 'ErrorLog ${APACHE_LOG_DIR}/error.log' >> /etc/apache2/sites-available/000-default.conf
echo 'CustomLog ${APACHE_LOG_DIR}/access.log combined' >> /etc/apache2/sites-available/000-default.conf
echo '</VirtualHost>' >> /etc/apache2/sites-available/000-default.conf

a2ensite 000-default.conf

systemctl restart apache2

```

Test Apache 

Open browser and go to page http://51.83.45.10/

 ## Define our parameters
 
 ```
 MYUSER=hephaistos
 MYPASSWORD=Gkh23hglxVd47ShG43jh3h
 MYDOMAINNAME=hello-world.hephaiscode.com
 MYEMAIL=hello-world@hephaiscode.com
 MYWEBFOLDER=WebSite
 ```
 
 ## Create user ${MYUSER}
 
 ```
useradd --shell /bin/false ${MYUSER}
echo ${MYUSER}:${MYPASSWORD} | chpasswd
mkdir /home/${MYUSER}
chown root /home/${MYUSER}
chmod go-w /home/${MYUSER}

```

Add directory

```
mkdir /home/${MYUSER}/

mkdir /home/${MYUSER}/${MYWEBFOLDER}_NOSSL

rm /home/${MYUSER}/${MYWEBFOLDER}_NOSSL/phpinfo.php
echo '<?php echo phpinfo();?>' >> /home/${MYUSER}/${MYWEBFOLDER}_NOSSL/phpinfo.php
rm /home/${MYUSER}/${MYWEBFOLDER}_NOSSL/index.html
echo '<html><body>Hello World! You are NOT secure! Please use <a href="https://hello-world.hephaiscode.com">SSL connexion</a>!</body></html>' >> /home/${MYUSER}/${MYWEBFOLDER}_NOSSL/index.html
chown -R ${MYUSER}:www-data /home/${MYUSER}/${MYWEBFOLDER}_NOSSL
chmod -R 750 /home/${MYUSER}/${MYWEBFOLDER}_NOSSL

```

## Install Domain Name

Create the host parameters for Apache and our domains **hello-world.hephaiscode.com**

```
MYAPACHECONFNOSSL=/etc/apache2/sites-available/${MYDOMAINNAME}_NOSSL.conf
rm ${MYAPACHECONFNOSSL}
echo "<VirtualHost *:80>" >> ${MYAPACHECONFNOSSL}
echo "Protocols h2 http/1.1" >> ${MYAPACHECONFNOSSL}
echo "ServerAdmin ${MYEMAIL}" >> ${MYAPACHECONFNOSSL}
echo "ServerName ${MYDOMAINNAME}" >> ${MYAPACHECONFNOSSL}
echo "ServerAlias ${MYDOMAINNAME}" >> ${MYAPACHECONFNOSSL}
echo "DocumentRoot /home/${MYUSER}/${MYWEBFOLDER}_NOSSL" >> ${MYAPACHECONFNOSSL}
echo "<Directory />" >> ${MYAPACHECONFNOSSL}
echo "AllowOverride All" >> ${MYAPACHECONFNOSSL}
echo "</Directory>" >> ${MYAPACHECONFNOSSL}
echo "<Directory /home/${MYUSER}/${MYWEBFOLDER}_NOSSL>" >> ${MYAPACHECONFNOSSL}
echo "Options Indexes FollowSymLinks MultiViews" >> ${MYAPACHECONFNOSSL}
echo "AllowOverride all" >> ${MYAPACHECONFNOSSL}
echo "Require all granted" >> ${MYAPACHECONFNOSSL}
echo "</Directory>" >> ${MYAPACHECONFNOSSL}
echo "LogLevel error" >> ${MYAPACHECONFNOSSL}
echo "ErrorLog /var/log/apache2/${MYDOMAINNAME}-nossl-error.log" >> ${MYAPACHECONFNOSSL}
echo "CustomLog /var/log/apache2/${MYDOMAINNAME}-nossl-access.log combined env=NoLog" >> ${MYAPACHECONFNOSSL}
echo "</VirtualHost>" >> ${MYAPACHECONFNOSSL}

a2ensite ${MYDOMAINNAME}_NOSSL.conf

systemctl restart apache2

```

## Hello World Test

Open browser and go to page http://hello-world.hephaiscode.com 

## Hello World Success

![Success](https://img.shields.io/badge/Hello%20World-OK-Green.svg)
