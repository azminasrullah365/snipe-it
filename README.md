![logo](https://snipeitapp.com/img/logos/snipe-it-logo-xs.png)
# Snipe-IT
How to self host snipe it
<br>
| Resource | Recommended                       |
|:---:|:---:|
| OS       | Ubuntu Server 22.04 LTS           |
| CPU      | 2 vCPU (4 if many users)          |
| RAM      | 4 GB (2 GB works for small teams) |
| Storage  | 40–50 GB                          |
| Network  | Bridge (vmbr0)                    |
<br>

## Step 1 — System Preparation and Update
```
sudo apt update && sudo apt upgrade -y
sudo apt install -y software-properties-common unzip curl git
```
## Step 2 — Install Web Stack (LEMP - Recommended)
```
sudo apt update && sudo apt upgrade -y
sudo apt install -y ca-certificates curl gnupg gi
```
### Install MariaDB
```
sudo apt install -y mariadb-server
sudo systemctl enable --now mariadb
sudo mysql_secure_installation
```
### Create database
```
sudo mariadb
```
Enter inside MariaDB
```
CREATE DATABASE snipeit CHARACTER SET utf8mb4;
CREATE USER 'snipeit'@'localhost' IDENTIFIED BY 'StrongPassword';
GRANT ALL PRIVILEGES ON snipeit.* TO 'snipeit'@'localhost';
FLUSH PRIVILEGES;
```
### Install PHP 8.2 + Extensions
```
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
sudo apt install -y \
php8.2 php8.2-fpm php8.2-cli php8.2-mysql php8.2-xml \
php8.2-mbstring php8.2-curl php8.2-zip php8.2-gd \
php8.2-bcmath php8.2-intl
```
Enable PHP
```
sudo systemctl enable --now php8.2-fpm
```
Verify
```
php -v
```
### Tune PHP (Required)
```
sudo nano /etc/php/8.2/fpm/php.ini
```
Set: 
```
memory_limit = 512M
upload_max_filesize = 100M
post_max_size = 100M
max_execution_time = 300
date.timezone = Asia/Jakarta
```
### Install Composer
```
cd /var/www
sudo git clone https://github.com/snipe/snipe-it.git
cd snipe-it
```
### Download Snipe-IT
```
cd /var/www
sudo git clone https://github.com/snipe/snipe-it.git
cd snipe-it
```
### Set Ownership before composer
```
sudo chown -R www-data:www-data /var/www/snipe-it
sudo chmod -R 755 /var/www/snipe-it
```
### Install PHP Dependencies
```
sudo -u www-data composer install --no-dev --prefer-dist
```
### Configure Environment
```
sudo -u www-data cp .env.example .env
sudo -u www-data nano .env
```
Set minimum required values:
```
APP_ENV=production
APP_DEBUG=false
APP_URL=http://VM_IP

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=snipeit
DB_USERNAME=snipeit
DB_PASSWORD=StrongPasswordHere

MAIL_MAILER=log
```
Generate Key
```
sudo -u www-data php artisan key:generate
```
Run Migrations
```
sudo -u www-data php artisan migrate --seed
```
Clear cache:
```
sudo -u www-data php artisan optimize:clearserver {
    listen 80;
    server_name _;
    root /var/www/snipe-it/public;

    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;
    }

    location ~ /\. {
        deny all;
    }
}
```
Enable Site
```
sudo ln -s /etc/nginx/sites-available/snipeit /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl reload nginx
```
### Final Permision check
```
sudo chown -R www-data:www-data /var/www/snipe-it
sudo chmod -R 755 /var/www/snipe-it
```
### Access Snipe-IT
```
http://VM_IP
```

### Common Post Install (Optional)
```
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

### Daily Backup (Optional but Recommended)
```
mysqldump snipeit > /var/backups/snipeit.sql
```
