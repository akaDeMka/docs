# Magento2 commands and configuration

**Change mode to production**  
From the Magento root directory, switch to production mode:

```bash
bin/magento deploy:mode:set production
```

**Enable JavaScript bundling:**

```bash
bin/magento config:set dev/js/enable_js_bundling 1
```

**Optimize bundling by minifying JavaScript files:**

```bash
bin/magento config:set dev/js/minify_files 1
```

**Enable cache busting on static file URLs.**  
This ensures users get the latest version of the assets anytime they update:

```bash
bin/magento config:set dev/static/sign 1
```

To **configure JavaScript bundling**, you must disable Javascript file merging. Bundling will not work as the merging of files excludes bundling:

```bash
bin/magento config:set dev/js/merge_files 0
```

**Enable move JavaScript to bottom:**

```bash
bin/magento config:set dev/js/move_script_to_bottom 1
```

**Deploy Static content for specific Theme**

```bash
php bin/magento setup:static-content:deploy --theme {Vendor}/{theme} en_US
```

**Resize Catalog images:**

```bash
php bin/magento catalog:images:resize
```

**Maintenance mode:**

```bash
bin/magento maintenance:enable
```

**Create admin user:**

```bash
bin/magento admin:user:create
```

**Disable optimizations:**

```bash
bin/magento config:set dev/js/enable_js_bundling 0
bin/magento config:set dev/js/merge_files 0
bin/magento config:set dev/js/minify_files 0
```

**Disable magento recapture**

```bash
php bin/magento config:set admin/captcha/enable 0
```

**Find and change variables:**

```bash
#Find settings variable
bin/magento config:show | grep web/unsecure
#configure variable
bin/magento config:set web/unsecure/base_static_url ''
```

## Configuration Menu:

Change Theme in Magento 2 <https://meetanshi.com/blog/change-theme-in-magento-2/>:

- Log in to Admin Panel
- Navigate to Content > Design > Configuration
- Click Edit theme
- In Applied Theme dropdown, select the theme that you want to change to.

**Social logins (Facebook, Google)**  
`Stories > Configuration > AMASTY EXTENSIONS > Social Login`  
**Google Captcha:**  
`Stores > Configuration > AMASTY EXTENSIONS > Google Invisible Captcha`  
`Stores > Configuration > SECURITY > Google reCAPTCHA Storefront`  
`Stores > Configuration > SECURITY > Google reCAPTCHA Admin Panel`  
**Configured magento API for anonymous user**
`Stores > Settings > Configuration > Services > Magento Web API > Web API Security`  
Then select Yes from the Allow Anonymous Guest Access menu.  
**SMTP email settings:**  
`Stores > Configuration > AMASTY EXTENSIONS > SMTP Email Settings`  
**Change admin email**  
`Stores > Configuration > WEBKUL > Marketplace > Admin Email-id`  
**Change to asynchronies mail sending**  
`Stores > Configuration > Sales >Sales Emails >General Settings`  
**Enable SSO:**  
`Stores->Configuration->Customer>Customer Configuration->Login Options and Enable SSO Login option`

### Articles

- [magento 1.9 - Separate Admin to new server with subdomain on AWS - Magento Stack Exchange](https://magento.stackexchange.com/questions/92431/separate-admin-to-new-server-with-subdomain-on-aws)
- [Magento 2 log rotation with logrotate - Magenaut - The Magento Astronaut](https://magenaut.com/magento-2-log-rotation-with-logrotate/)
