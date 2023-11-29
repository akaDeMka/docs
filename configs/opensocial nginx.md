# OpenSocial Nginx install

## Requirments

- Ubuntu 20.04
- Nginx 1.18+,
- PHP 7.4,
- Composer 2.x,
- MariaDB 10.4

>!!! MPORTANT NOTICE  !!! Drupal 8.9 does not support SimpleSamlPHP 1.19.1. Thus we use downgraded version 1.18

## Steps to install

1. Install environment

    ```bash
    #Update Ubuntu distro
    apt upgrade
    #install nfs utils
    apt install nfs-common
    #Install Nginx
    apt install nginx
    #Install PHP 7.4 + php-fpm for nginx
    add-apt-repository ppa:ondrej/php
    apt install php7.4 php7.4-xml php7.4-gd php7.4-mysql php7.4-fpm php7.4-curl php7.4-mbstring php7.4-zip
    #Install Composer
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php composer-setup.php --install-dir=/usr/local/bin --filename=composer
    ```

1. Install MariaDB Server:

    ```bash
    apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'
    add-apt-repository 'deb [arch=amd64,arm64,ppc64el] https://mirrors.nxthost.com/mariadb/repo/10.4/ubuntu focal main'
    apt install mariadb-server
    #optional
    mysql_secure_installation
    ```

## Configure OpenSocial

1. Configuring database

    ```bash
    #Creating database for opensocial
    sudo mysql -uroot -p
    CREATE DATABASE <db_name>;
    #Create user and grant access for DB
    CREATE USER '<db_user>'@'%' IDENTIFIED BY '<db_passwd>';
    GRANT ALL ON <db_name>.* TO '<db_user>'@'%';
    FLUSH PRIVILEGES;
    exit;
    ```

    Import provided sql dump:

    ```bash
    mkdir <path_to_mount_point>
    mount <remote_path> <local_path>
    mysql -uroot -p <db_passwd> < <local_path>/dump.sql
    ```

1. Configure server for deploy:

    ```bash
    #create user
    adduser gitlab
    mkdir /var/www/<app>-social
    addgroup <app>
    gpasswd -M gitlab,root,www-data <app>
    chown -R gitlab:<app> /var/www/<app>
    chmod 750 -R /home/gitlab
    mkdir /home/gitlab/.ssh
    cp <local_path>/authorized_keys /home/gitlab/.ssh/
    chmod 700 -R /home/gitlab/.ssh
    chown -R gitlab:gitlab /home/gitlab/.ssh
    cp <local_path>/gitlab /etc/sudoers.d/
    ```

1. Configure Nginx. Create file configuration for opensocial site:

    ```bash
    cp <local_path>/<app>.conf /etc/nginx/sites-available/
    rm -rf /etc/nginx/sites-enabled/*
    ln -s /etc/nginx/sites-available/<app>.conf /etc/nginx/sites-enabled/
    systemctl restart nginx
    systemctl restart php7.4-fpm
    ```

    <https://www.nginx.com/resources/wiki/start/topics/recipes/drupal/>

1. Install and configure opensocial:

    ```bash
    #su gitlab
    # If new install
    #cd /var/www
    #composer create-project goalgorilla/social_template:10.1.6 /var/www/<app> --no-interaction
    cd /var/www/<app>
    # If new install
    #git clone <url> 
    git pull
    composer install
    mkdir /var/www/<app>/sites/default/files
    sudo cp -r <local_path>/files/ /var/www/<app>/html/sites/default/files/
    sudo cp <local_path>/settings.php /var/www/<app>/html/sites/default/
    # Allow css and js - check maybe only on first install need.
    /var/www/<app>/vendor/bin/drush -y config-set system.performance css.preprocess 0
    /var/www/<app>/vendor/bin/drush -y config-set system.performance js.preprocess 0
    sudo chown -R www-data:<app> /var/www/<app>/html
    ```

## Configure SimpleSaml

- Update nginx configuration - add new section:

    ```bash
    location /simplesaml {
        alias /var/www/<app>/vendor/simplesamlphp/simplesamlphp/www ;
        index index.php;
        
        location ~ ^(?<prefix>/simplesaml)(?<phpfile>.+?\.php)(?<pathinfo>/.*)?$ {
            include          fastcgi_params;
            fastcgi_pass     fastcgi_backend;
            fastcgi_param SCRIPT_FILENAME $document_root$phpfile;
            # Must be prepended with the baseurlpath
            fastcgi_param SCRIPT_NAME simplesaml/$phpfile;
            fastcgi_param PATH_INFO $pathinfo if_not_empty;
        }
    #for style and js working section
        location ~* \.(ico|jpg|jpeg|png|gif|svg|js|css|swf|eot|ttf|otf|woff|woff2)$ {
            add_header Cache-Control "public";
            add_header X-Frame-Options "SAMEORIGIN";
            expires +1y;
        }
    }
    ```

- While deploy implement simplesaml config (copy files with config) in files:

    ```bash
    <Opensocial folder>/vendor/simplesamlphp/simplesamlphp/config/authsources.php
    <Opensocial folder>/vendor/simplesamlphp/simplesamlphp/config/config.php
    <Opensocial folder>/vendor/simplesamlphp/simplesamlphp/metadata/saml20-idp-remote.php
    ```

- > Important !!! Modify \<app>\vendor\simplesamlphp\simplesamlphp\config\config.php. Find section DATA STORE CONFIGURATION. Set the next values:

    ```bash
    /*
        - Configure the data store for SimpleSAMLphp.
        *
        - - 'phpsession': Limited datastore, which uses the PHP session.
        - - 'memcache': Key-value datastore, based on memcache.
        - - 'sql': SQL datastore, using PDO.
        - - 'redis': Key-value datastore, based on redis.
        *
        - The default datastore is 'phpsession'.
        */
        'store.type'                    => 'sql',

        /*
        - The DSN the sql datastore should connect to.
        *
        - See <http://www.php.net/manual/en/pdo.drivers.php> for the various
        - syntaxes.
        */
        //'store.sql.dsn'                 => 'sqlite:/path/to/sqlitedatabase.sq3'
        'store.sql.dsn'                 => 'mysql:host=localhost;dbname=magesaml',

        /*
        - The username and password to use when connecting to the database.
        */
        'store.sql.username' => 'magesaml',
        'store.sql.password' => 'magesaml',

        /*
        - The prefix we should use on our tables.
        */
        'store.sql.prefix' => 'SimpleSAMLphp',
    ```
