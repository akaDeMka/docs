# CarbonCalc Nginx

## Requirments

- Ubuntu 20.04
- Nginx 1.18+
- python 3.8
- pip3
- MariaDB 10.4
- unit 1.25+ (nginx-unit)

## Install components

```bash
#Update Ubuntu distro
    apt update
    apt upgrade
#Install Nginx
    apt install nginx
#Python 3.8 has already installed on ubuntu 20.04
#install pip3
    apt install python3-pip
#Install mariaDB
    apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'
    add-apt-repository 'deb [arch=amd64,arm64,ppc64el] https://mirrors.nxthost.com/mariadb/repo/10.4/ubuntu focal main'
    apt install mariadb-server
#Install Unit
    curl -sL https://nginx.org/keys/nginx_signing.key | apt-key add -
    echo "deb https://packages.nginx.org/unit/ubuntu/ focal unit" > /etc/apt/sources.list.d/unit.list
    echo "deb-src https://packages.nginx.org/unit/ubuntu/ focal unit" >> /etc/apt/sources.list.d/unit.list
    apt update
    apt install unit
    apt install unit-python3.8
    systemctl restart unit
```

MariaDB articles:<br>

- <https://mariadb.com/kb/en/installing-mariadb-deb-files/#updating-the-mariadb-apt-repository-to-a-new-major-release>
- <https://www.8host.com/blog/bystraya-ustanovka-mariadb-v-ubuntu-20-04/> </br>
Unit articles: <https://unit.nginx.org/installation/#ubuntu-2004>

## Configure instance

1. Configuring databases:

    ```bash
    #Creating database
    sudo mysql -uroot -p
    CREATE DATABASE <dbname>;
    CREATE USER '<dbuser>'@'%' IDENTIFIED BY '<dbpassword>';
    GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, REFERENCES, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, EVENT, TRIGGER ON <dbname>.* TO '<dbuser>'@'%';
    FLUSH PRIVILEGES;
    exit;
    ```

1. Get project:

    ```bash
    cd /var/www
    git clone <url>
    git checkout master
    git pull
    ```

1. Install project dependencies:

    ```bash
    apt install default-libmysqlclient-dev
    pip3 install -r /var/www/<app>/requirements.txt
    ```

1. Configure project:

    ```bash
    cd /var/www/<app>
    python3 manage.py makemigrations
    python3 manage.py migrate
    cp /configs/.env /var/www/<app>/<name>/
    ```

1. Configure unit:

    ```bash
    #create project config
    echo "{
      "applications": {
        "<app>": {
            "type": "python 3.8",
            "processes": 2,
            "path": "/var/www/<app>",
            "module": "<app>.wsgi",
            "environment": {
                "DJANGO_SETTINGS_MODULE": "<app>.settings_dev"
            }
        }
      },
      "listeners": {
        "127.0.0.1:8400": {
          "application": "<app>"
        }
      }
    }" > /etc/unit/<app>.json
    #start unit thread
    unitd --control unix:/var/run/unit.control.sock --log /var/log/unit.log
    #activate config
    cd /etc/unit
    curl -X PUT --data-binary @<app>.json --unix-socket /var/run/unit.control.sock http://localhost/config
    ```

    >More info: <https://unit.nginx.org/configuration/>

    **Additional commands:**

    ```bash
    #stop unit thread
    pkill unitd
    #to check application work
    curl http://127.0.0.1:8400/api/v1/<app>Start
    #Remove config
    curl -X DELETE --data-binary @<app>.json --unix-socket /var/run/unit.control.sock http://localhost/config
    ```

1. Configure nginx:

    ```bash
    # create nginx config
    echo "server {
        listen 80;
        server_name $hostname;
        return 301 https://$host$request_uri;
    }

    server {
        listen 443 ssl http2;
        server_name $hostname;
        error_log /var/log/nginx/$hostname.error.log;

        ssl_certificate /etc/cert/$hostname.crt;
        ssl_certificate_key /etc/cert/$hostname.key;
        ssl_protocols TLSv1.2 TLSv1.3;

        location / {
            proxy_pass http://127.0.0.1:8400/;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forward-Proto http;
            proxy_set_header X-Nginx-Proxy true;
            proxy_redirect off;
        }  
    } > /etc/nginx/sites-available/<app>.conf
    # Implement configuration
    rm -rf /etc/nginx/sites-enabled/*
    ln -s /etc/nginx/sites-available/<app>.conf /etc/nginx/sites-enabled/
    systemctl restart nginx
    ```

1. Check application work:

    ```bash
    curl https://hostname/api/v1/<app>Start
    ```
