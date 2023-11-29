# Magento SimpleSamlPHP SP Nginx

**Working nginx configuration:**

```bash
#TERA SSO SECTION
location /simplesaml {
    alias <<path to application>>/vendor/simplesamlphp/simplesamlphp/www/;
     
    location ~ ^(?<prefix>/simplesaml)(?<phpfile>.+?\.php)(?<pathinfo>/.*)?$ {
        include          fastcgi_params;
        fastcgi_pass     fastcgi_backend;
        fastcgi_param SCRIPT_FILENAME $document_root$phpfile;
        # Must be prepended with the baseurlpath
        fastcgi_param SCRIPT_NAME /simplesaml$phpfile;
        fastcgi_param PATH_INFO $pathinfo if_not_empty;
        #custom simplesaml config_dir location
        fastcgi_param SIMPLESAMLPHP_CONFIG_DIR "/var/simplesamlphp/config";
    }
}
```

Config files place defined by SIMPLESAMLPHP_CONFIG_DIR = /var/simplesamlphp/config/  
Metadata folder configured in config.php file

```bash
'metadatadir' => '/var/simplesamlphp/metadata',
```
