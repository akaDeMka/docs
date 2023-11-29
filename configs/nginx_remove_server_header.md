# Nginx Remove server header

1. Install nginx-extras package alongside nginx:

    ```bash
    sudo apt install nginx-extras
    ```

1. Open nginx.conf and add following lines inside http block:

    ```bash
    more_clear_headers Server;
    server_tokens off;
    ```

1. Restart nginx

    ```bash
    sudo systemctl restart nginx
    ```
