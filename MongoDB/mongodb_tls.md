# MongoDB TLS

As a more simple solution Nginx TLS certificate in middle provided

1. Add Nginx configuration for port streaming like:

    ```bash
    stream {
        upstream stream_backend {
            server 127.0.0.1:27017;
        }
    }
    server {
        listen                27020 ssl;
        proxy_pass            stream_backend;
    
        ssl_certificate       /<path-to-certificate>/fullchain.pem;
        ssl_certificate_key   //<path-to-privat-key>/privkey.pem;
    }
    ```

    to file mongo-stream.nginx
2. Add to nginx.conf before http section:

    ```bash
    include /etc/nginx/sites-available/mongo-stream.nginx;
    ```
