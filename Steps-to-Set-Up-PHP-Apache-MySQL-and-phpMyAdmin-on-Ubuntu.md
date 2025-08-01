# Steps to Set Up PHP, Apache, MySQL, and phpMyAdmin on Ubuntu

Setting up a LAMP stack (Linux, Apache, MySQL, PHP) on Ubuntu is a common task for developers. This guide will walk you through the steps to install and configure Apache, MySQL, PHP, and phpMyAdmin on an Ubuntu system.


## Step 1: Install Apache

### Update and Upgrade Your System
First, ensure your system is up to date:

```bash
sudo apt update && sudo apt upgrade
```

### Install Apache

Next, install Apache:

```bash
sudo apt install apache2
```

### Setup Firewall

Set up the Uncomplicated Firewall (UFW) to allow public access on default web ports for HTTP and HTTPS:

```bash
sudo ufw allow 'Apache'
```

Enbale Apache Full


```bash
sudo ufw allow 'Apache Full'
```

allow 22/tcp port for SSH

```bash
sudo ufw allow 22/tcp
```

Show all allowed connections

```bash
sudo ufw status
```

Enable UFW

```bash
sudo ufw enable
```

List all allowed connections

```bash
sudo ufw app list
```

Apache management commands

Check the status of Apache
```bash
sudo systemctl status apache2
```

Start Apache
```bash
sudo systemctl start apache2
```

Stop Apache
```bash
sudo systemctl stop apache2
```

Restart Apache
```bash
sudo systemctl restart apache2
```

Reload Apache
```bash
sudo systemctl reload apache2
```

Check Version of Apache
```bash
sudo apachectl -v
```

## Steps 2 Install MySQL

Install MySQL

```bash
sudo apt install mysql-server
```

Secure MySQL Installation

```bash
sudo mysql_secure_installation
```

Follow the prompts to secure your installation.

Yes -> 0 -> Yes -> Yes -> Yes

Set the marchine password for MySQL and ignore 


### Create a new user and grant privileges

Access Mysql

```bash
sudo mysql
```
Creat User 

Replace dbusername with your desired username and dbpassword with your desired password

```bash
CREATE USER 'dbusername'@'localhost' IDENTIFIED BY 'dbpassword';
```

Grant privileges

```bash
GRANT ALL PRIVILEGES ON *.* TO 'dbusername'@'localhost';
```

Flush privileges

```bash
FLUSH PRIVILEGES;
```

Exit MySQL

```bash
exit
```

### MySQL Management Commands

Check the status of MySQL

```bash
sudo systemctl status mysql
```

Start MySQL

```bash
sudo systemctl start mysql
```

Stop MySQL

```bash
sudo systemctl stop mysql
```

Restart MySQL

```bash
sudo systemctl restart mysql
```

## Steps 3 Install PHP

Install PHP

```bash
sudo apt install software-properties-common -y
```

```bash
sudo add-apt-repository ppa:ondrej/php
```

```bash
sudo apt install php8.2-fpm php8.2 libapache2-mod-php8.2 php8.2-common php8.2-mysql php8.2-xml php8.2-xmlrpc php8.2-curl php8.2-gd php8.2-imagick php8.2-cli php8.2-imap php8.2-mbstring php8.2-opcache php8.2-soap php8.2-zip php8.2-intl php8.2-bcmath unzip -y
```

Check the version of PHP

```bash
php -v
```

Configure PHP

```bash
sudo nano /etc/php/8.2/apache2/php.ini
```

Change the following lines

```bash
upload_max_filesize = 32M 
post_max_size = 48M 
memory_limit = 256M 
max_execution_time = 600 
max_input_vars = 3000 
max_input_time = 1000
```

Restart Apache

```bash
sudo systemctl restart apache2
```

### 4. Install PhpMyAdmin

```bash
sudo apt install phpmyadmin
```

Restart Apache

```bash
sudo systemctl restart apache2
```

Cofigure PHPMYADMIN with Apache

Enable the configuration:

```bash
sudo a2enconf phpmyadmin
```

Restart Apache

```bash
sudo systemctl restart apache2
```

Access Php
  
  ```bash
  http://sever-ip/phpmyadmin
  ```





# ADDITIONAL SERVER CONFIGURATION 

### How to add cron job in Ubuntu

```bash
crontab -e
```

Add the following line to run the script every 5 minutes

```bash
*/5 * * * * /usr/bin/php /var/www/html/cron.php
```

Save and exit

### How to add a instll composer in Ubuntu 

```bash
sudo apt install composer
```

Chcek Composer version

```bash
composer --version
```

### How to link an push file with git

File are stored in /var/www/html

```bash
cd /var/www/html/ProgectName
```

Generate token from github vist : https://github.com/settings/tokens

Clone with github token 

```bash
sudo git clone https://<ACCESSTOKEN>@github.com/username/ProjectName.git
```

Or you can use WinSCP to upload files to the server

### How to Point a domain

User ClodeFare to point to the server ip ten add this 

```bash
sudo nano /etc/apache2/sites-available/mikrolink.conf
```

```bash
<VirtualHost *:80>
    ServerAdmin webmaster@mikrol.ink
    ServerName mikrol.ink
    DocumentRoot /var/www/html/MiroTikLink/public

    <Directory /var/www/html/MiroTikLink>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

```bash
sudo a2ensite mikrolink.conf
```

```bash
sudo systemctl reload apache2
```

### To fix Url not found

```bash
sudo apt update
sudo apt install libapache2-mod-rewrite
sudo a2enmod rewrite
sudo service apache2 restart
```

Happy Coding! :smile:
