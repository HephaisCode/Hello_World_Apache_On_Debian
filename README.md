# Hello World Apache On Debian

You need : 

- Server on Debian (linux distribution) with root access
- DN (Domain Name) point on Server IP (example hello-world.hephaiscode.com on 51.83.45.10)
- Terminal/console to enter instruction

## Connect to server 

```
ssh -l root 51.83.45.10 
```

And enter your root password 

## Update your server

```
apt-get update
apt-get -y upgrade
apt-get -y dist-upgrade
```

## Install Apache

```
apt-get -y install apache2
systemctl restart apache2
```

## Install PHP

## Install Domain Name
```
mkdir /home/helloworld
rm /etc/apache2/sites-available/helloworld_ws.conf
echo "<VirtualHost *:80>" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "Protocols h2 http/1.1" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "ServerAdmin contact@helloworld.io" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "ServerName hello-world.hephaiscode.com" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "ServerAlias hello-world.hephaiscode.com" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "DocumentRoot /home/helloworld" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "<Directory />" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "AllowOverride All" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "</Directory>" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "<Directory /home/helloworld>" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "Options Indexes FollowSymLinks MultiViews" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "AllowOverride all" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "Require all granted" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "</Directory>" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "LogLevel error" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "ErrorLog /var/log/apache2/helloworld-error.log" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "CustomLog /var/log/apache2/helloworld-ssl-access.log combined env=NoLog" >> /etc/apache2/sites-available/helloworld_ws.conf
echo "</VirtualHost>" >> /etc/apache2/sites-available/helloworld_ws.conf
a2ensite helloworld_ws.conf
systemctl restart apache2
```

## Hello World Script

```
echo "<html><body>Hello World!</body></html>" >> /home/helloworld/index.html
```

## Hello World Success
