# "Hello World!" with Apache PHP and Cerbot on Debian 10

[![OS badge](https://img.shields.io/badge/OS-Debian-red.svg)](https://www.debian.org)
[![Server badge](https://img.shields.io/badge/Server-Apache-blue.svg)](https://httpd.apache.org)
[![SSL badge](https://img.shields.io/badge/SSL-certbot-blue.svg)](https://certbot.eff.org)
[![Language badge](https://img.shields.io/badge/Language-PHP-blue.svg)](https://php.net)
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
apt-get -y install openssl

systemctl restart apache2

```

Active Apache modules

```
a2enmod ssl
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

mkdir /var/www/ssl

openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout /var/www/ssl/apache.key -out /var/www/ssl/apache.crt -subj "/C=FR/ST=KNOWHERE/L=KNOWHERE/O=Global Security/OU=IT Department/CN=Apache"

rm /etc/apache2/sites-available/000-default-le-ssl.conf
echo '<IfModule mod_ssl.c>' >> /etc/apache2/sites-available/000-default-le-ssl.conf
echo '<VirtualHost *:443>' >> /etc/apache2/sites-available/000-default-le-ssl.conf
echo 'ServerAdmin webmaster@localhost' >> /etc/apache2/sites-available/000-default-le-ssl.conf
echo 'DocumentRoot /var/www/html' >> /etc/apache2/sites-available/000-default-le-ssl.conf
echo 'ErrorLog ${APACHE_LOG_DIR}/error.log' >> /etc/apache2/sites-available/000-default-le-ssl.conf
echo 'CustomLog ${APACHE_LOG_DIR}/access.log combined' >> /etc/apache2/sites-available/000-default-le-ssl.conf
echo "SSLEngine On" /etc/apache2/sites-available/000-default-le-ssl.conf
echo 'SSLCipherSuite ALL:!DH:!EXPORT:!RC4:+HIGH:+MEDIUM:!LOW:!aNULL:!eNULL' >> /etc/apache2/sites-available/000-default-le-ssl.conf
echo "SSLCertificateFile /var/www/ssl/apache.crt" >> /etc/apache2/sites-available/000-default-le-ssl.conf
echo "SSLCertificateKeyFile /var/www/ssl/apache.key" >> /etc/apache2/sites-available/000-default-le-ssl.conf
echo '</VirtualHost>' >> /etc/apache2/sites-available/000-default-le-ssl.conf
echo '</IfModule>' >> /etc/apache2/sites-available/000-default-le-ssl.conf

a2ensite 000-default-le-ssl.conf

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

Open browser and go to page https://51.83.45.10/
 
 ## Install PHP

Install PHP with Apache

```
apt-get -y install php7.3-fpm

a2dismod php7.3
a2enconf php7.3-fpm
a2enmod proxy_fcgi

rm /var/www/html/phpinfo.php
echo "<?php echo phpinfo();?>" > /var/www/html/phpinfo.php

systemctl restart apache2

```

Test Apache 

Open browser and go to page http://51.83.45.10/phpinfo.php

Open browser and go to page https://51.83.45.10/phpinfo.php

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

Add directories

```
mkdir /home/${MYUSER}/

mkdir /home/${MYUSER}/${MYWEBFOLDER}_NOSSL

rm /home/${MYUSER}/${MYWEBFOLDER}_NOSSL/phpinfo.php
echo '<?php echo phpinfo();?>' >> /home/${MYUSER}/${MYWEBFOLDER}_NOSSL/phpinfo.php
rm /home/${MYUSER}/${MYWEBFOLDER}_NOSSL/index.html
echo '<html><body>Hello World! You are NOT secure! Please use <a href="https://hello-world.hephaiscode.com">SSL connexion</a>!</body></html>' >> /home/${MYUSER}/${MYWEBFOLDER}_NOSSL/index.html
chown -R ${MYUSER}:www-data /home/${MYUSER}/${MYWEBFOLDER}_NOSSL
chmod -R 750 /home/${MYUSER}/${MYWEBFOLDER}_NOSSL

mkdir /home/${MYUSER}/ssl

openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout /home/${MYUSER}/ssl/${MYDOMAINNAME}.key -out /home/${MYUSER}/ssl/${MYDOMAINNAME}.crt -subj "/C=FR/ST=KNOWHERE/L=KNOWHERE/O=Global Security/OU=IT Department/CN=${MYDOMAINNAME}"

chown -R ${MYUSER}:www-data /home/${MYUSER}/ssl
chmod -R 750 /home/${MYUSER}/ssl

mkdir /home/${MYUSER}/${MYWEBFOLDER}_SSL
echo '<?php echo phpinfo();?>' >> /home/${MYUSER}/${MYWEBFOLDER}_SSL/phpinfo.php
echo '<html><body>Hello World! You are secure!</body></html>' >> /home/${MYUSER}/${MYWEBFOLDER}_SSL/index.html
chown -R ${MYUSER}:www-data /home/${MYUSER}/${MYWEBFOLDER}_SSL
chmod -R 750 /home/${MYUSER}/${MYWEBFOLDER}_SSL

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

MYAPACHECONFSSL=/etc/apache2/sites-available/${MYDOMAINNAME}_SSL.conf
rm ${MYAPACHECONFSSL}
echo "<IfModule mod_ssl.c>" >> ${MYAPACHECONFSSL}
echo "<VirtualHost *:80>" >> ${MYAPACHECONFSSL}
echo "Protocols h2 http/1.1" >> ${MYAPACHECONFSSL}
echo "ServerAdmin ${MYEMAIL}" >> ${MYAPACHECONFSSL}
echo "ServerName ${MYDOMAINNAME}" >> ${MYAPACHECONFSSL}
echo "ServerAlias ${MYDOMAINNAME}" >> ${MYAPACHECONFSSL}
echo "DocumentRoot /home/${MYUSER}/${MYWEBFOLDER}_SSL" >> ${MYAPACHECONFSSL}
echo "<Directory />" >> ${MYAPACHECONFSSL}
echo "AllowOverride All" >> ${MYAPACHECONFSSL}
echo "</Directory>" >> ${MYAPACHECONFSSL}
echo "<Directory /home/${MYUSER}/${MYWEBFOLDER}_SSL>" >> ${MYAPACHECONFSSL}
echo "Options Indexes FollowSymLinks MultiViews" >> ${MYAPACHECONFSSL}
echo "AllowOverride all" >> ${MYAPACHECONFSSL}
echo "Require all granted" >> ${MYAPACHECONFSSL}
echo "</Directory>" >> ${MYAPACHECONFSSL}
echo "LogLevel error" >> ${MYAPACHECONFSSL}
echo "ErrorLog /var/log/apache2/${MYDOMAINNAME}-ssl-error.log" >> ${MYAPACHECONFSSL}
echo "CustomLog /var/log/apache2/${MYDOMAINNAME}-ssl-access.log combined env=NoLog" >> ${MYAPACHECONFSSL}
echo "SSLEngine On" >> ${MYAPACHECONFSSL}
echo 'SSLCipherSuite ALL:!DH:!EXPORT:!RC4:+HIGH:+MEDIUM:!LOW:!aNULL:!eNULL' >> ${MYAPACHECONFSSL}
echo "SSLCertificateFile /home/${MYUSER}/ssl/${MYDOMAINNAME}.crt" >> ${MYAPACHECONFSSL}
echo "SSLCertificateKeyFile /home/${MYUSER}/ssl/${MYDOMAINNAME}.key" >> ${MYAPACHECONFSSL}
echo "</VirtualHost>" >> ${MYAPACHECONFSSL}
echo "</IfModule>" >> ${MYAPACHECONFSSL}

a2ensite ${MYDOMAINNAME}_SSL.conf

systemctl restart apache2

```

## Add ssl certificat by Certbot

```
apt-get -y install certbot python-certbot-apache

certbot --agree-tos -n --no-eff-email --apache --redirect --email ${MYEMAIL} -d ${MYDOMAINNAME}

systemctl restart apache2

```

## Hello World Test

Open browser and go to page http://hello-world.hephaiscode.com 

Open browser and go to page http://hello-world.hephaiscode.com/phpinfo.php

Open browser and go to page https://hello-world.hephaiscode.com (certificat is valided by certbot)

Open browser and go to page https://hello-world.hephaiscode.com/phpinfo.php (certificat is valided by certbot)

## Hello World Success

![Success](https://img.shields.io/badge/Hello%20World-OK-Green.svg)
