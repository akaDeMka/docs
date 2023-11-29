# Varnish implementation for Magento2

## Install Varnish

<https://www.varnish-software.com/developers/tutorials/installing-varnish-ubuntu/>

```bash
curl -s -L https://packagecloud.io/varnishcache/varnish65/gpgkey | sudo apt-key add -
sudo tee /etc/apt/sources.list.d/varnishcache_varnish.list > /dev/null <<-EOF
deb https://packagecloud.io/varnishcache/varnish65/$ID/ $VERSION_CODENAME main
EOF
sudo tee /etc/apt/preferences.d/varnishcache > /dev/null <<-EOF
Package: varnish varnish-* hitch
Pin: release o=packagecloud.io/varnishcache/*
Pin-Priority: 1000
EOF
```

## Update magento admin console configuration

<https://devdocs.magento.com/guides/v2.4/config-guide/varnish/config-varnish-magento.html>  
cache purging <https://devdocs.magento.com/guides/v2.4/config-guide/varnish/use-varnish-cache.html>

```bash
cd <magento_folder>
bin/magento setup:config:set --http-cache-hosts=varnish_server:6081
```

### Useful commands

- Get varnish statistics:

    ```bash
    sudo varnishstat
    ```

- Get varnish requests log:

    ```bash
    sudo varnishlog -g request
    ```

- Edit varnish demon configuration:

    ```bash
    sudo systemctl edit --full varnish.service
    ```

- Get only headers with curl:

    ```bash
    curl -I -X GET  http://ip
    ```

- Check varnish configuration

    ```bash
    varnishd -C -f /etc/varnish/default.vcl
    ```

- Reload varnish configuration [Reload Varnish VCL without losing cache data (ttias.be)](https://ma.ttias.be/reload-varnish-vcl-without-losing-cache-data/)

    ```bash
    sudo systemctl reload varnish
    ```

- Check backend servers status

    ```bash
    sudo varnishadm backend.list
    #more detailed
    sudo varnishadm backend.list -p
    ```

- Purge Magento 2 cache:

    ```bash
    curl -i -X PURGE http://<varnish_ip>:6081 -H 'X-Magento-Tags-Pattern: .*'
    ```

### Varnish configuration

divided in 2 parts:

1. **Varnish demon configuration**  
    In demon configuration update section:

    ```bash
    ExecStart=/usr/sbin/varnishd \
            -a /run/varnish/varnish.sock,user=varnish,group=varnish,mode=666 \
            -a internal_ip:6081 \
            -p feature=+http2 \
            -f /etc/varnish/default.vcl \
            -s malloc,1G
    -a /run/varnish/varnish.sock,user=varnish,group=varnish,mode=666
    ```

    - use socket instead of port (speed increase) for nginx in front of varnish.
    - s malloc,1G - increase memory for cache up to 1Gb
    - a internal_ip:6081 - use port 6081 on internal interface (mostly for varnish cache management) for magento purge cache

2. **Varnish server configuration**  
    Example taken from <https://www.varnish-software.com/developers/tutorials/configuring-varnish-magento/> (it works better that Magento2 default configuration)
    Modifications:

   - Update hosts and ports accordingly
   - Update section (close to AWS ALB):

        ```bash
        .probe = {
                .url = "/health_check.php";
        .expected_response = 200;
                .timeout = 10s;
                .interval = 30s;
                .window = 5;
                .threshold = 2;
        }
        ```

Varnish configuration for dynamic backend <https://docs.varnish-software.com/varnish-cache-plus/vmods/goto/>. It gives opportunity to use AWS ELB as varnish backend.

### Articles

- <https://devdocs.magento.com/guides/v2.4/config-guide/varnish/config-varnish.html>
- <https://www.varnish-software.com/developers/tutorials/installing-varnish-ubuntu/>
- <https://info.varnish-software.com/blog/understanding-timeouts-varnish-cache>
- <https://info.varnish-software.com/blog/understanding-varnish-cache-memory-usage>
- <https://varnish-cache.org/docs/6.0/reference/varnishd.html#ref-varnishd-options>
