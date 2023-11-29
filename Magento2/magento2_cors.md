# Magento CORS header REST

Configuration added CORS to specified urls. Can be activated by including to nginx server configuration.  
Example:

```bash
include /var/www/tera/nginx-rest-cors.conf;
```

nginx configuration added to repository (nginx-rest-cors.conf).
><span style="color:red">ATTENTION!</span> It can be use only for test purpose in this configuration.

To increase security you have to uncomment if section:

```bash
#Uncoment and replace checked urls 
#if ($http_origin ~ '^https?://(localhost|www\.yourdomain\.com|www\.yourotherdomain\.com)') {
# set $cors_origin $http_origin;
#}
```

replace site urls in the section with necessary ones and change default value for $cors_origin to ""

```bash
#Replace
set $cors_origin ""
```

The logic can be changed according <https://gist.github.com/Stanback/7145487>
