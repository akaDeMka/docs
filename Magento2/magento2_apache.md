# Magento Apache Install

**Magento 2.4.2 requirments:**

- Ubuntu 20.04
- Apache2 2.4+
- PHP 7.4
- Composer 2.x
- MariaDB 10.4
- Elastic Search 7.x

## Install Magento 2

1. Update Ubuntu distro:

    ```bash
    sudo apt update
    sudo apt upgrade
    ```

2. Install Apache 2:

    ```bash
    sudo apt install apache2
    ```

3. Install PHP 7.4:

    ```bash
    sudo apt install software-properties-common
    sudo add-apt-repository ppa:ondrej/php
    sudo apt install php7.4 libapache2-mod-php7.4 php7.4-common php7.4-gmp php7.4-curl php7.4-soap php7.4-bcmath php7.4-intl php7.4-mbstring php7.4-xmlrpc php7.4-mysql php7.4-gd php7.4-xml php7.4-cli php7.4-zip
    ```

4. Configure php.ini (/etc/php/7.4/apache2/)  
Change option `short_open_tag = on`  
To switch PHP version (in case of multiple PHP versions)

    ```bash
    sudo update-alternatives --config php
    ```

5. Install Composer:

    ```bash
    #sudo apt install unzip - for what?
    sudo php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
    ```

6. Install MariaDB Server(update):

    ```bash
    sudo apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'
    sudo add-apt-repository 'deb [arch=amd64,arm64,ppc64el] https://mirrors.nxthost.com/mariadb/repo/10.4/ubuntu focal main'
    sudo apt install mariadb-server
    sudo mysql_secure_installation
    ```

    - <https://mariadb.com/kb/en/installing-mariadb-deb-files/#updating-the-mariadb-apt-repository-to-a-new-major-release>
    - Take from here <https://www.8host.com/blog/bystraya-ustanovka-mariadb-v-ubuntu-20-04/>

7. Install Elastic Search:

    ```bash
    curl -fsSL <https://artifacts.elastic.co/GPG-KEY-elasticsearch> | sudo apt-key add -
    echo "deb <https://artifacts.elastic.co/packages/7.x/apt> stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
    sudo apt update
    sudo apt install elasticsearch
    sudo systemctl start elasticsearch
    sudo systemctl enable elasticsearch
    ```

Take from here <https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-elasticsearch-on-ubuntu-20-04-ru>

## Configure Magento 2

1. Get sources - request appropriate version of SQL dumps for Magento DB and WebKul Marketplace (WebProvise should provide)
1. Configuring databases

    ```bash
    # Creating databases magento2 and marketplace
    sudo mysql -uroot -p
    CREATE DATABASE <db>;
    CREATE DATABASE <db_market>;
    # Create user and grant access for both DBs
    CREATE USER '<db_user>'@'%' IDENTIFIED BY '<db_passwd>';
    GRANT ALL ON <db>.*TO '<db_user>'@'%' WITH GRANT OPTION;
    GRANT ALL ON <db_market>.* TO '<db_user>'@'%' WITH GRANT OPTION;
    FLUSH PRIVILEGES;
    exit;
    ```

1. Import provided sql dumps:

    ```bash
    mysql -uroot -p  <db> < main.sql
    mysql -uroot -p  <db_market> < market.sql
    ```

1. Update base database URLs:

    ```bash
    sudo mysql -uroot -p
    use <db>;
    # Set local URL (see Apache configuration as well)
    UPDATE core_config_data SET value = '<url>' WHERE path in ('web/unsecure/base_url', 'web/secure/base_url');
    # Elastic search configuration
    UPDATE core_config_data SET value = '127.0.0.1' WHERE path='amasty_elastic/connection/server_hostname';
    UPDATE core_config_data SET value = '9200' WHERE path='amasty_elastic/connection/server_port';
    UPDATE core_config_data SET value = 'amasty_elastic' WHERE path='amasty_elastic/connection/engine';
    ```

1. Disable reCaptcha - optional:

    ```bash
    # Variant 1
    sudo mysql -uroot -p
    use <db>;
    UPDATE core_config_data SET value = 0 WHERE path in (
    'msp_securitysuite_recaptcha/backend/enabled',
    'msp_securitysuite_recaptcha/frontend/enabled',
    'aminvisiblecaptcha/general/enabledCaptcha',
    'aminvisiblecaptcha/general/enabledCaptchaForGuestsOnly',
    'aminvisiblecaptcha/amasty/customForm',
    'admin/captcha/enable',
    'customer/captcha/enable',
    'marketplace/general_settings/captcha');
    exit;
    # Variant 2 - cache:clean after
    cd /var/www/html/magento2
    bin/magento security:recaptcha:disable-for-user-login
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

1. Configure Apach  
    - Modify /etc/apache2/apache2.conf (find section '<Directory /var/www/>'):  

        ```bash
        <Directory /var/www/>  
            Options Indexes FollowSymLinks 
            AllowOverride 
            All Require all granted
        </Directory>
        ```

    - Create file configuration for Magento site ('magento2.conf' for example)

        ```bash
        sudo echo "
        <VirtualHost *:80>
        ServerAdmin <admin@touchsoft.by>
        DocumentRoot /var/www/html/magento2
        ServerName <url>
        ServerAlias <url>

        ErrorLog ${APACHE_LOG_DIR}/error.magento2.log
        CustomLog ${APACHE_LOG_DIR}/access.magento2.log combined

        <Directory /var/www/html/magento2>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Order allow,deny
        allow from all
        </Directory>

        # Allow encoded slashes

        AllowEncodedSlashes NoDecode
        </VirtualHost>" >  /etc/apache2/sites-available/magento2.conf
        ```

    - Save configuration file and after that define new host in /etc/hosts  
        `127.0.0.1 YOU_SERVER.touchsoft.by`
    - Enable new site configuration and mod_rewrite:

        ```bash
        sudo a2enmod rewrite
        sudo a2ensite magento2
        sudo systemctl restart apache2
        ```

1. Configure Magento 2

    - Grant rights for folder recursively

        ```bash
        cd /var/www/html/magento2
        sudo chmod 777 -R ./
        ```

    - Update project dependencies:

        ```bash
        cd /var/www/html/magento2
        sudo composer install
        # To update the Magento database schema and data:
        sudo bin/magento setup:upgrade
        # To compile Magento sources:
        sudo bin/magento setup:di:compile
        # To enable developer mode:
        sudo bin/magento deploy:mode:set developer
        # To compile static content:
        sudo bin/magento setup:static-content:deploy -f
        # To clean cache
        sudo bin/magento cache:clean
        ```

    >Sometimes it is not enough to clean magento cache only. You have to restart apache2 and mariadb services.
