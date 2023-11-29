# Python app Apache install

## Requirments:

- Ubuntu 20.04,
- Apache 2.4+ (MOD_WSGI),
- python 3.8,
- MariaDB 10.3
  
1. Install server environment:

    ```bash
    sudo apt install python3.8
    ```

1. Configure SQL database (MariaDB)

    ```bash
    #mariaDb database already installed
    sudo mysql -uroot -p
    CREATE DATABASE <base>;
    CREATE USER '<user>'@'localhost' IDENTIFIED BY '<password>';
    GRANT ALL ON <base>.* TO '<user>'@'%' WITH GRANT OPTION;
    FLUSH PRIVILEGES;
    exit
    sudo echo "[client]
    database = <base>
    user = <user>
    password = <password>
    default-character-set = utf8
    host = localhost
    port = 3306" >/etc/mysql/conf.d/app.cnf
    ```

1. Create python environment for application

    ```bash
    #created python venv
    cd /var/www/html/<app>
    python3 -m venv calc-env
    source calc-env/bin/activate
    #in venv
    python -m pip install -r requirements.txt
    python3 manage.py makemigrations
    python3 manage.py migrate
    deactivate
    ```

1. Configure apache 2.4+

    ```bash
    #apache 2.4+ already installed
    sudo apt install libapache2-mod-wsgi-py3
    echo "<VirtualHost *:80>
        ServerName <app>
        WSGIDaemonProcess <app>.local processes=2 threads=15 display-name=%{GROUP} python-home=/var/www/html/<app>/<app-env> python-path=/var/www/html/<app>
        WSGIProcessGroup <app>.local
        WSGIScriptAlias / /var/www/html/<app>/<name>/wsgi.py
     
        LogLevel info
        DocumentRoot /var/www/html/<app>
     
        <Directory /var/www/html/<app>/<name>>
            WSGIScriptReloading On
            <Files wsgi.py>
                Require all granted
            </Files>
        </Directory>
     
        WSGIMapHEADToGET Auto
     
    </VirtualHost>" > /etc/apache2/sites-available/<app>.conf
    a2enconf <app>
    systemctl reload apache2
    ```

1. Registry localhost url - optional

    ```bash
    # Add to /etc/hosts tera-calc url
    echo "127.0.0.1 localhost <app>.local" >/etc/hosts
    ```
