# url-shortener-yourls
A step by step deployment guide for [YOURLS](https://github.com/YOURLS/YOURLS).  

Yourls is an open-source project allowing to run Your Own URL Shortener, on your server, using the domain or subdomain of your choice.

I have deployed my own instance of YOURLS and shared in this repo a step by step guide based on the [official docs](https://yourls.org/docs/).  

Provision a Ubuntu Linux server and: 

###  A 
Install dependencies

```
apt install apache2
apt-get install php
apt-get install php-mysql
apt-get install mysql-server
apt install unzip
```
###  B 
configure a local db

```
mysql -u root -p
CREATE USER 'giancarlo'@'localhost' IDENTIFIED BY '<my-password>';
grant all privileges on *.* to 'giancarlo'@'localhost';
mysql -u giancarlo -p 
create database yourls;
```
###  C 
get the latest release [source code link](https://github.com/YOURLS/YOURLS/releases) and configure the app.


```
cd var/www/html
wget https://github.com/YOURLS/YOURLS/archive/refs/tags/1.9.1.zip
unzip 1.9.1.zip
cp -r YOURLS-1.9.1/. .
rm -rf YOURLS-1.9.1
cp user/config-sample.php config.php
nano config.php 
```
Populate config.php with db credentials, yourls site, and yourls credentials as per [docs](https://yourls.org/docs/guide/essentials/configuration). 

If root owns everything under/var/www/html/user ```chown -R www-data.www-data * ``` so that yourls can write to the conf file 

Give r/w to config.php  ``` chmod 666 config.php ```

Populate ``` nano /var/www/html/.htaccess ```

with 
```
# BEGIN YOURLS
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^.*$ /yourls-loader.php [L]
</IfModule>
# END YOURLS
```


Create a copy of apache2 config file 
```
cp /etc/apache2/sites-available/000-default.conf shortener.conf
```
Populate with the following to match config.php 

```
DocumentRoot /var/www/html
ServerName yourdomain.root
```

And at the end 
```
<Directory /var/www/html>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride All
                Order allow,deny
                allow from all
</Directory>
```

### D
Open port 80, then visit yourdomain.root/admin to configure yourls 
