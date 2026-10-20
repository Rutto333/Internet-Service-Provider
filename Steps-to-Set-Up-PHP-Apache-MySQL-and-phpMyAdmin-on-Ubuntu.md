# Steps to Set Up PHP, Apache, MySQL, and phpMyAdmin on Ubuntu

Setting up a LAMP stack (Linux, Apache, MySQL, PHP) on Ubuntu is a common task for developers. This guide will walk you through the steps to install and configure Apache, MySQL, PHP, and phpMyAdmin on an Ubuntu system.

## Step 1: Install Apache

### Update and Upgrade Your System
First, ensure your system is up to date:

```bash
sudo apt update && sudo apt upgrade
```

### Install Apache
```bash
sudo apt install apache2
```

### Setup Firewall
Set up the Uncomplicated Firewall (UFW) to allow public access on default web ports for HTTP and HTTPS:

```bash
sudo ufw allow 'Apache'
```

Enable Apache Full:
```bash
sudo ufw allow 'Apache Full'
```

Allow SSH port:
```bash
sudo ufw allow 22/tcp
```

Show all allowed connections:
```bash
sudo ufw status
```

Enable UFW:
```bash
sudo ufw enable
```

List all allowed connections:
```bash
sudo ufw app list
```

### Apache management commands
Check the status of Apache:
```bash
sudo systemctl status apache2
```

Start Apache:
```bash
sudo systemctl start apache2
```

Stop Apache:
```bash
sudo systemctl stop apache2
```

Restart Apache:
```bash
sudo systemctl restart apache2
```

Reload Apache:
```bash
sudo systemctl reload apache2
```

Check Apache version:
```bash
sudo apachectl -v
```

---

## Step 2: Install MySQL

Install MySQL:
```bash
sudo apt install mysql-server
```

Secure MySQL installation:
```bash
sudo mysql_secure_installation
```
Follow prompts: **Yes -> 0 -> Yes -> Yes -> Yes**

### Create a new user and grant privileges
Access MySQL:
```bash
sudo mysql
```

Create user:
```bash
CREATE USER 'dbusername'@'localhost' IDENTIFIED BY 'dbpassword';
```

Grant privileges:
```bash
GRANT ALL PRIVILEGES ON *.* TO 'dbusername'@'localhost';
```

Flush privileges:
```bash
FLUSH PRIVILEGES;
```

Exit MySQL:
```bash
exit
```

### MySQL Management Commands
Check MySQL status:
```bash
sudo systemctl status mysql
```

Start MySQL:
```bash
sudo systemctl start mysql
```

Stop MySQL:
```bash
sudo systemctl stop mysql
```

Restart MySQL:
```bash
sudo systemctl restart mysql
```

---


## Step 3: Install PHP

Install PHP:
```bash
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
sudo apt install php8.4-fpm php8.4 libapache2-mod-php8.4 php8.4-common php8.4-mysql php8.4-xml php8.4-xmlrpc php8.4-curl php8.4-gd php8.4-imagick php8.4-cli php8.4-imap php8.4-mbstring php8.4-opcache php8.4-soap php8.4-zip php8.4-intl php8.4-bcmath unzip -y
```

Check PHP version:
```bash
php -v
```

Configure PHP:
```bash
sudo nano /etc/php/8.4/apache2/php.ini
```
Change:
```
upload_max_filesize = 32M 
post_max_size = 48M 
memory_limit = 256M 
max_execution_time = 600 
max_input_vars = 3000 
max_input_time = 1000
```

Restart Apache:
```bash
sudo systemctl restart apache2
```

---


## Step 4: Install phpMyAdmin
```bash
sudo apt install phpmyadmin
sudo systemctl restart apache2
```

Enable configuration:
```bash
sudo a2enconf phpmyadmin
sudo systemctl restart apache2
```

Access in browser:
```
http://server-ip/phpmyadmin
```

---

# ADDITIONAL SERVER CONFIGURATION 

### How to add a cron job in Ubuntu
```bash
crontab -e
```
Run every 5 minutes:
```bash
*/5 * * * * /usr/bin/php /var/www/html/cron.php
```

---

### Install Composer
```bash
sudo apt install composer
composer --version
```

---

### Push files with Git
```bash
cd /var/www/html/ProjectName
```
Clone with token:
```bash
sudo git clone https://<ACCESSTOKEN>@github.com/username/ProjectName.git
```

Or upload with WinSCP.

---

### Point a domain
```bash
sudo nano /etc/apache2/sites-available/mikrolink.conf
```
Example:
```apache
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

Enable site:
```bash
sudo a2ensite mikrolink.conf
sudo systemctl reload apache2
```

---

### Files Permission

**Set execute permission for a script:**
```bash
sudo chmod +x /usr/local/bin/filename
```

**Set standard read, write, execute permissions:**
```bash
chmod 755 file
```
Breakdown:
- **Owner** → read, write, execute
- **Group** → read, execute
- **Others** → read, execute

**Numeric permission values:**
- `7` = read (4) + write (2) + execute (1)
- `6` = read (4) + write (2)
- `5` = read (4) + execute (1)
- `4` = read only

**Symbolic examples:**
```bash
chmod u+x file      # Add execute for owner
chmod g+w file      # Add write for group
chmod o-r file      # Remove read for others
chmod a+x file      # Add execute for everyone
```

---

### Change file ownership with `chown`

**Basic usage:**
```bash
sudo chown user file            # Change owner only
sudo chown user:group file      # Change owner and group
sudo chown :group file          # Change group only
```

**Example for web server files:**
```bash
sudo chown www-data:www-data /var/www/html/index.php
```

**Recursively change ownership for a folder:**
```bash
sudo chown -R user:group /path/to/folder
```

---

### Fix "URL not found"
```bash
sudo apt update
sudo apt install libapache2-mod-rewrite
sudo a2enmod rewrite
sudo service apache2 restart
```

---

Happy Coding! 😄
