# Wordpress Sandbox
1. install ubuntu server and dependencies:
    ```bash
    sudo apt update && apt upgrade -y && reboot

    #Install dependencies
    sudo apt install mariadb-server curl gnupg2 ca-certificates lsb-release software-properties-common -y
    sudo mysql_secure_installation

    #Install php 8.1
    sudo add-apt-repository ppa:ondrej/php
    sudo apt update
    sudo apt install php8.1 php8.1-bcmath php8.1-curl php8.1-imagick php8.1-intl php8.1-mbstring php8.1-mysql php8.1-xml php8.1-zip -y
    ```
2. Create DB:
    ```bash
    su root
    mysql -root -p
    CREATE DATABASE blablabla_DB CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_520_ci;
    CREATE USER '<user>'@'localhost' IDENTIFIED BY '<password>';
    GRANT ALL PRIVILEGES ON blablabla_DB.* TO '<user>'@'localhost';
    ```
3. Configure apache2:
    ```bash
    <VirtualHost *:80>
        ServerName <URL>
        DocumentRoot /var/www/<URL>
        ErrorLog /var/log/apache2/<URL>/apache.error.log
        CustomLog /var/log/apache2/<URL>/apache.access.log common
        <Directory /var/www/<URL>>
            Options FollowSymLinks
            AllowOverride Limit Options FileInfo Indexes
            DirectoryIndex index.php
            Require all granted
        </Directory>
        <Directory /var/www/<URL>/wp-content>
            Options FollowSymLinks
            Require all granted
        </Directory>
    </VirtualHost>
    ```
4. Configure .htaccess. <br>
    Copy htaccess.example to .htaccess. <br>
    Add to .htaccess:
    ```bash
    php_value short_open_tag 1
    php_value memory_limit 512M
    php_value upload_max_filesize 25M
    php_value post_max_size 27M
    ```
5. Create/edit wp-config (add DB variables)
6. Configure deployment and run code, DB and images deploy
## Some site tweaks
### To fix issue with proxy server in front of site (https)
1. You need to pass some information to the back end Apache so that WordPress can detect when it’s being accessed via https. Here’s the headers 
you need to send:
    ```bash
    RequestHeader set X-Forwarded-Proto "https"
    RequestHeader set X-Forwarded-Port "443"
    ```
2. Then you can add this to your wp-config.php file to detect when the website is loaded on https:
    ```bash
    if (!empty($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER ['HTTP_X_FORWARDED_PROTO'] === 'https') 
    {
        $_SERVER['HTTPS'] = 'on';
    }
    ```
### Apache2 enable modules to configure browser cache:
```bash
a2enmod headers
a2enmod expires
systemctl restart apache2
```