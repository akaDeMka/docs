# PHP. Calculating pm.max_children

>**Example:** if our cloud server has 4 GB RAM and a MariaDB database service is running as well that consumes at least 1 GB our best aim is to get  
4 - 1 - 0,5 (marge) GB = 2,5 GB RAM or 2560 Mb.  
pm.max_children brings us to 2560 Mb / 60 Mb = 42 max_children

### Used configuration

We have made the following changes in our www.conf file in the php-fpm pool:

```bash
pm.max_children = 40
pm.start_servers = 15
pm.min_spare_servers = 15
pm.max_spare_servers = 25
pm.max_requests = 500
```

Restart the php-fpm service and see if the server behaves in a correct manner and allocates memory as configured.
