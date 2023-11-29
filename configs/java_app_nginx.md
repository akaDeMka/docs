# Java api app

## Steps to create environment

**Build dependencies:**

- OpenJDK 17.x
- Maven for build or use maven wrapper (in repository)  

**Host Dependencies:**

- Ubuntu 20.04
- OpenJRE 17.x
- nginx 1.18+

### Configure host

```bash
#Update Ubuntu distro
apt-get upgrade -y
#Install Java 17 openjdk-17-jdk  ?
apt-get install -y openjdk-17-jre
#Install Nginx
apt-get install nginx
```

### Build application

```bash
chmod +x mvnw && ./mvnw clean install
```

### Start application

```bash
java -DSERVER_ADDRESS=<server_host> -DSERVER_PORT=<server_port> \
-DDB_URL=r2dbc:mariadb://<db_host>:<db_port>/<database> \
-DDB_USER=<db_user> -DDB_PASSWORD=<db_password> \
-DBARCODELOOKUP_KEY=<barcode> \
-Dreactor.netty.http.server.accessLogEnabled=true \
-DLog4jContextSelector=org.apache.logging.log4j.core.async.AsyncLoggerContextSelector \
-Dlogs.proprietiesFile=<path_to_log4j_propities> \ 
-jar target/<app>.jar
```

### Nginx configuration (proxy server)
if nginx proxy planned use server_host=127.0.0.1 and server_port=8000 (example)
nginx config:

```bash
sudo echo "server {
      listen 80;
      server_name <app_url>;
      return 307 https://$host$request_uri;
    }
 
server {
    listen 443 ssl;
    server_name <app_url>;
    ssl_certificate        D:\certs/_local.crt;
    ssl_certificate_key    D:\certs/_local.key;
    ssl_protocols          TLSv1.3 TLSv1.2;
    location / {
        proxy_pass          http://<app_url>:8080/;
    }
}" >  /etc/nginx/sites-available/<app>.conf
rm -rf /etc/nginx/sites-enabled/*
ln -s /etc/nginx/sites-available/<app>.conf /etc/nginx/sites-enabled/
systemctl restart nginx
```
