# Magento 2 Nginx install

Magento 2.4.2 requirments:

- Ubuntu 20.04
- Nginx 1.18+
- PHP 7.4
- Composer 2.x
- MariaDB 10.4
- Elastic Search 7.9

## Installiation
1. Install environment:

    ```bash
    #Update Ubuntu distro
    apt update
    apt upgrade
    
    #Install Nginx
    apt install nginx
    
    #Install PHP 7.4
    #php-fpm for nginx
    sudo apt install software-properties-common
    #add repository not actual for ubuntu 20.04
    #sudo add-apt-repository ppa:ondrej/php
    sudo apt install php7.4 php7.4-common php7.4-gmp php7.4-curl php7.4-soap php7.4-bcmath php7.4-intl php7.4-mbstring php7.4-xmlrpc php7.4-mysql php7.4-gd php7.4-xml php7.4-cli php7.4-zip php7.4-fpm
    
    #Install Composer
    sudo php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
    ```

1. Install MariaDB Server(update):

    ```bash
    sudo apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'
    sudo add-apt-repository 'deb [arch=amd64,arm64,ppc64el] https://mirrors.nxthost.com/mariadb/repo/10.4/ubuntu focal main'
    sudo apt install mariadb-server
    sudo mysql_secure_installation
    ```

    <https://mariadb.com/kb/en/installing-mariadb-deb-files/#updating-the-mariadb-apt-repository-to-a-new-major-release>  
    Take from here <https://www.8host.com/blog/bystraya-ustanovka-mariadb-v-ubuntu-20-04/>

1. Install Elastic Search:

    ```bash
    curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
    echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
    sudo apt update
    sudo apt install elasticsearch
    sudo systemctl start elasticsearch
    sudo systemctl enable elasticsearch
    ```

    Take from here <https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-elasticsearch-on-ubuntu-20-04-ru>

## Configure Magento

1. Get sources - Request appropriate version of SQL dumps for Magento DB and WebKul Marketplace (WebProvise should provide)
1. Configuring databases
    - database rights:

        ```bash
        SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, TRIGGER
        #Creating databases magento2 and marketplace
        sudo mysql -uroot -p
        CREATE DATABASE <db>;
        CREATE DATABASE <db_market>;
        #Create user and grant access for both DBs
        CREATE USER '<db_user>'@'%' IDENTIFIED BY '<db_passwd>';
        GRANT ALL ON <db>.* TO '<db_user>'@'%';
        GRANT ALL ON <db_market>.* TO '<db_user>'@'%';
        #simplesaml database
        CREATE DATABASE magesaml;
        CREATE USER 'magesaml'@'%' IDENTIFIED BY 'magesaml';
        GRANT ALL ON magesaml.* TO 'magesaml'@'%';
        FLUSH PRIVILEGES;
        exit;
        ```

    - Import provided sql dumps:

        ```bash
        mysql -uroot -p  <db> < /home/gitlab/magento2/main.sql
        mysql -uroot -p  <db_market> < /home/gitlab/magento2/market.sql
        ```

    - Update base database URLs:

        ```bash
        sudo mysql -uroot -p
        use <db>;
        #Set local URL (see Apache configuration as well)
        UPDATE core_config_data SET value = '<url>' WHERE path in ('web/unsecure/base_url', 'web/secure/base_url');
        #Elastic search configuration
        UPDATE core_config_data SET value = '127.0.0.1' WHERE path='amasty_elastic/connection/server_hostname';
        UPDATE core_config_data SET value = '9200' WHERE path='amasty_elastic/connection/server_port';
        UPDATE core_config_data SET value = 'amasty_elastic' WHERE path='amasty_elastic/connection/engine';
        ```

1. Modify app/etc/env.php (check 'db' and 'session' sections)  
In my case the path to session folder is '/var/www/html/magento2/tmp'

    ```bash
    sudo echo "
    <?php
    return [
        'backend' => [
            'frontName' => 'm2admin'
        ],
        'crypt' => [
            'key' => '2a1c5e20a651efdde33823452bde0591'
        ],
        'db' => [
            'table_prefix' => '',
            'connection' => [
                'default' => [
                    'host' => 'localhost:3306',
                    'dbname' => '<db>',
                    'username' => '<db_user>',
                    'password' => '<db_passwd>',
                    'model' => 'mysql4',
                    'engine' => 'innodb',
                    'initStatements' => 'SET NAMES utf8;',
                    'active' => '1',
                    'driver_options' => [
                        1014 => false
                    ]
                ],
                'marketplace' => [
                    'host' => 'localhost:3306',
                    'dbname' => '<db_market>',
                    'username' => '<db_user>',
                    'password' => '<db_passwd>',
                    'model' => 'mysql4',
                    'engine' => 'innodb',
                    'initStatements' => 'SET NAMES utf8;',
                    'active' => '1'
                ]
            ]
        ],
        'resource' => [
            'default_setup' => [
                'connection' => 'default'
            ],
            'marketplace' => [
                'connection' => 'marketplace'
            ]
        ],
        'x-frame-options' => 'SAMEORIGIN',
        'MAGE_MODE' => 'developer',
        'session' => [
            'save' => 'files',
            'save_path' => '/var/www/html/magento2/tmp'
        ],
        'cache' => [
            'frontend' => [
                'default' => [
                    'id_prefix' => 'bc9_'
                ],
                'page_cache' => [
                    'id_prefix' => 'bc9_'
                ]
            ]
        ],
        'lock' => [
            'provider' => 'db',
            'config' => [
                'prefix' => ''
            ]
        ],
        'cache_types' => [
            'config' => 1,
            'layout' => 1,
            'block_html' => 1,
            'collections' => 1,
            'reflection' => 1,
            'db_ddl' => 1,
            'compiled_config' => 1,
            'eav' => 1,
            'customer_notification' => 1,
            'config_integration' => 1,
            'config_integration_api' => 1,
            'google_product' => 1,
            'full_page' => 1,
            'config_webservice' => 1,
            'translate' => 1,
            'vertex' => 1,
            'amasty_shopby' => 1
        ],
        'downloadable_domains' => [
            '<url>'
        ],
        'install' => [
            'date' => 'Thu, 23 Jan 2020 23:07:39 +0000'
        ],
        'queue' => [
            'consumers_wait_for_messages' => 1
        ]
    ];" > /var/www/html/magento2/app/etc/env.php
    ```

1. Configure Nginx  
    - Create file configuration for Magento site

        ```bash
        sudo echo "
        upstream fastcgi_backend {
        server  unix:/run/php/php7.4-fpm.sock;
        }
        
        server {
        listen 80;
        listen [::]:80;
        server_name <url>;
        return 301 https://\$host\$request_uri;
        }
        
        server {
        listen 443 ssl http2;
        listen [::]:443 ssl http2;
        server_name <url>;
        
        ssl_certificate        /etc/cert/digitalmara.com.bundle.crt;
        ssl_certificate_key    /etc/cert/digitalmara.com.key;
        ssl_protocols          TLSv1.3 TLSv1.2;
        
        set $MAGE_ROOT /var/www/html/magento2; 
        include /var/www/html/magento2/nginx.conf.sample;
        }" >  /etc/nginx/sites-available/magento2.conf
        ```

1. Enable new site configuration <https://devdocs.magento.com/guides/v2.4/install-gde/prereq/php-settings.html>:

    ```bash
    #Magento optimizations
    sed -i "/memory_limit/c\memory_limit = 1G" /etc/php/7.4/fpm/php.ini
    sed -i "/max_execution_time/c\max_execution_time = 1800" /etc/php/7.4/fpm/php.ini
    sed -i "/zlib.output_compression/c\zlib.output_compression = On" /etc/php/7.4/fpm/php.ini
    sed -i "/memory_limit/c\memory_limit = 1G" /etc/php/7.4/cli/php.ini
    sed -i "/max_execution_time/c\max_execution_time = 1800" /etc/php/7.4/cli/php.ini
    sed -i "/zlib.output_compression/c\zlib.output_compression = On" /etc/php/7.4/cli/php.ini
    sed -i "/short_open_tag/c\short_open_tag = On" /etc/php/7.4/fpm/php.ini
    sed -i "/short_open_tag/c\short_open_tag = On" /etc/php/7.4/cli/php.ini
    sed -i "/realpath_cache_size/c\realpath_cache_size = 10M" /etc/php/7.4/fpm/php.ini
    sed -i "/realpath_cache_size/c\realpath_cache_size = 10M" /etc/php/7.4/cli/php.ini
    sed -i "/realpath_cache_ttl/c\realpath_cache_ttl=7200" /etc/php/7.4/fpm/php.ini
    sed -i "/realpath_cache_ttl/c\realpath_cache_ttl=7200" /etc/php/7.4/cli/php.ini
    #enable and configure OpCache
    sed -i "/opcache.enable/c\opcache.enable=1" /etc/php/7.4/fpm/php.ini
    sed -i "/opcache.memory_consumption/c\opcache.memory_consumption=512" /etc/php/7.4/fpm/php.ini
    sed -i "/opcache.max_accelerated_files/c\opcache.max_accelerated_files=60000" /etc/php/7.4/fpm/php.ini
    sed -i "/opcache.consistency_checks/c\opcache.consistency_checks=0" /etc/php/7.4/fpm/php.ini
    sed -i "/opcache.validate_timestamps/c\opcache.validate_timestamps=0" /etc/php/7.4/fpm/php.ini
    sed -i "/opcache.enable_cli/c\opcache.enable_cli=1" /etc/php/7.4/fpm/php.ini
    sed -i "/opcache.enable/c\opcache.enable=1" /etc/php/7.4/cli/php.ini
    sed -i "/opcache.memory_consumption/c\opcache.memory_consumption=512" /etc/php/7.4/cli/php.ini
    sed -i "/opcache.max_accelerated_files/c\opcache.max_accelerated_files=60000" /etc/php/7.4/cli/php.ini
    sed -i "/opcache.consistency_checks/c\opcache.consistency_checks=0" /etc/php/7.4/cli/php.ini
    sed -i "/opcache.validate_timestamps/c\opcache.validate_timestamps=0" /etc/php/7.4/cli/php.ini
    sed -i "/opcache.enable_cli/c\opcache.enable_cli=1" /etc/php/7.4/cli/php.ini
    #replace php-fpm user
    sed -i "/user =/c\user = <deploy_user>" /etc/php/7.4/fpm/pool.d/www.conf
    sed -i "/group =/c\group = <deploy_user_group>" /etc/php/7.4/fpm/pool.d/www.conf
    #Increase php-fpm children number - depends on server resources
    sed -i "/pm =/c\pm = static" /etc/php/7.4/fpm/pool.d/www.conf
    sed -i "/pm.max_children =/c\pm.max_children = 10" /etc/php/7.4/fpm/pool.d/www.conf
    sed -i "/pm.max_requests =/c\pm.max_requests = 500" /etc/php/7.4/fpm/pool.d/www.conf
    systemctl restart php7.4-fpm
    #Nginx tuning
    sed -i "/worker_connections/c\worker_connections 1024;" /etc/nginx/nginx.conf
    rm -rf /etc/nginx/sites-enabled/*
    ln -s /etc/nginx/sites-available/magento2.conf /etc/nginx/sites-enabled/
    systemctl restart nginx
    ```

    <https://devdocs.magento.com/guides/v2.4/install-gde/prereq/nginx.html>
1. Configure Magento
    - Grant rights for folder recursively

        ```bash
        cd /var/www/html/magento2
        sudo chown -R user:user ./ 
        ```

    - Update project dependencies:

        ```bash
        cd /var/www/html/magento2
        sudo composer install 
        #To update the Magento database schema and data:
        sudo bin/magento setup:upgrade
        #To compile Magento sources:
        sudo bin/magento setup:di:compile
        #To compile static content:
        sudo bin/magento setup:static-content:deploy -f
        #To clean cache
        sudo bin/magento cache:clean 
        ```

    Sometimes it is not enough to clean magento cache only. You have to restart nginx and mariadb services.

1. Install Magento cronjob:

    ```bash
    bin/magento cron:install [--force]
    ```

1. SimpleSaml. Update nginx configuration - add new section:

    ```bash
    #SSO SECTION
    location /simplesaml {
        alias /var/www/html/magento2/vendor/simplesamlphp/simplesamlphp/www/;
    
        location ~ ^(?<prefix>/simplesaml)(?<phpfile>.+?\.php)(?<pathinfo>/.*)?$ {
            include          fastcgi_params;
            fastcgi_pass     fastcgi_backend;
            fastcgi_param SCRIPT_FILENAME $document_root$phpfile;
            # Must be prepended with the baseurlpath
            fastcgi_param SCRIPT_NAME /simplesaml$phpfile;
            fastcgi_param PATH_INFO $pathinfo if_not_empty;
            #custom simplesaml config_dir location
            fastcgi_param SIMPLESAMLPHP_CONFIG_DIR "/var/simplesamlphp/config";
        }
    }
    ```

1. While deploy implement simplesaml config (copy files with config) in files:

    ```bash
    /var/simplesamlphp/config/authsources.php
    /var/simplesamlphp/config/config.php
    /var/simplesamlphp/metadata/saml20-idp-remote.php
    ```

1. <span style="color:red">Important !!!</span>
Modify /var/simplesamlphp/config/config.php.  
Find section DATA STORE CONFIGURATION. Set the next values:

    ```bash
    /*
        * Configure the data store for SimpleSAMLphp.
        *
        * - 'phpsession': Limited datastore, which uses the PHP session.
        * - 'memcache': Key-value datastore, based on memcache.
        * - 'sql': SQL datastore, using PDO.
        * - 'redis': Key-value datastore, based on redis.
        *
        * The default datastore is 'phpsession'.
        */
        'store.type'                    => 'sql',
    
        /*
        * The DSN the sql datastore should connect to.
        *
        * See http://www.php.net/manual/en/pdo.drivers.php for the various
        * syntaxes.
        */
        //'store.sql.dsn'                 => 'sqlite:/path/to/sqlitedatabase.sq3'
        'store.sql.dsn'                 => 'mysql:host=localhost;dbname=magesaml',
    
        /*
        * The username and password to use when connecting to the database.
        */
        'store.sql.username' => 'magesaml',
        'store.sql.password' => 'magesaml',
    
        /*
        * The prefix we should use on our tables.
        */
        'store.sql.prefix' => 'SimpleSAMLphp',
    ```

