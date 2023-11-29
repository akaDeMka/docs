# Python App microservice

## Preconditions

- Ubuntu 20.04 (on VMware for Windows users),
- Apache2,
- Python 3.9 (PIP and PipEnv are also need)
- django 3.1.2
- djangorestframework 3.12.2

## Install dependencies

```bash
# Update Ubuntu repo:
sudo apt-get update
# Install Apache 2:
sudo apt-get install apache2
# Instal Python 3.9
sudo apt install python3.9
# Install PIP
sudo apt-get -y install python3-pip
# Install PIPENV
sudo apt install software-properties-common python-software-properties
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install pipenv
```

## Main steps

1. Checkout master branch into /var/www/html:

    ```bash
    sudo git clone <URL>
    git checkout master
    git pull
    ```

1. Create virtual environment:

    ```bash
    # Change path
    cd <projectdir>
    # Create Python virtual environment
    sudo pipenv install
    ```

1. Activate environment:

    ```bash
    pipenv shell
    This command allow to enter into special mode. You'll detect id by the next marker in command line '()' To exit from this mode press (Ctrl+D) or type 'exit()'
    ```

1. Make migrations in the activated environment (create DDL instructions for database):

    ```bash
    python3 manage.py makemigrations
    ```

1. Launch migrations in the activated environment (execute DDL instructions for database):

    ```bash
    python3 manage.py migrate
    ```

1. Setup apache2 config:

    ```bash
    cd /etc/apache2/sites-available
    sudo nano app.conf
    ```

    Use this config:

    ```bash
    <VirtualHost *:8000>
        ServerName app.local
        DocumentRoot /var/www/html/app
        # Relax Apache security settings
        <Directory /var/www/html/app>
            Allow from all
            Options -MultiViews
            # Uncomment this if you're on Apache >= 2.4
            Require all granted
        </Directory>
    </VirtualHost>
    ```

    Save file
1. Update /etc/hosts file to enable host for App:

    ```bash
    sudo nano /etc/hosts
    ```

    And add one the following line:

    ```bash
    # local environment:
    127.0.0.1  app.local
    # WSL2 (Windows Subsystem Linux)
    <current ip address of the eth0 interface>  app.local
    ```

1. Enable apache configuration and restart Apache2

    ```bash
    sudo a2ensite app
    sudo service apache2 restart
    ```

1. Run App

    ```bash
    cd <projectdir>
    pipenv shell
    python3 manage.py runserver app.local:8000 --settings=App.settings.[prod|dev]
    # There should be something like that:
    Watching for file changes with StatReloader
    Performing system checks...

    System check identified no issues (0 silenced).
    March 23, 2021 - 14:13:18
    Django version 3.1.2, using settings 'App.settings'
    Starting development server at <http://app.local:8000/>
    Quit the server with CONTROL-C.
    Option: MariaDB setup
    ```

1. Create database, user and grant privileges:

    ```bash
    CREATE DATABASE app_db;
    CREATE USER 'db_user'@'%' IDENTIFIED BY 'db_passwd';
    GRANT ALL ON app_db.* TO 'db_user'@'%' WITH GRANT OPTION;
    FLUSH PRIVILEGES;
    ```

1. Modify mariadb.cnf:

    ```bash
    sudo nano /etc/mysql/mariadb.cnf
    ```

    And put next lines:

    ```bash
    [client]
    database = app_db
    user = db_user
    password = db_passwd
    default-character-set = utf8
    host = localhost
    port = 3306
    ```

1. Modify settings.py:

    ```json
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql',
            'NAME': 'app',
            'OPTIONS': {
                'read_default_file': '/etc/mysql/mariadb.cnf',
            },
        }
    }
    ```

1. Make sure that package 'python3-dev' has installed on your Ubuntu: ???

    ```bash
    sudo apt install python3-dev
    ```

1. Run pipenv shell:

    ```bash
    pipenv shell
    ```

1. Install mysql-client:

    ```bash
    pipenv install mysqlclient
    ```

1. Create and run Django migrations (inside shell):

    ```bash
    python3 manage.py makemigrations
    python3 manage.py migrate
    ```
