# OpenSocial commands

**Update OpenSocial** (<https://www.drupal.org/docs/distributions/open-social/updating>)

```bash
cd /var/www/<app>
composer require goalgorilla/open_social:10.2.5 -w
export PATH="/var/www/<app>/vendor/bin:$PATH"
drush cache-rebuild
drush updb
chown -R www-data:<user_g> /var/www/<app>
```

**Disable maintenance mode:**

```bash
drush state:set system.maintenance_mode 0 --input-format=integer
drush cache:rebuild
```

**For new instance:**

```bash
# Only for new instance without db
composer require drush
vendor /bin/drush site-install social --db-url=mysql://<db_user> :<db_pass>@localhost:3306 /<app>
```

**Create new user:**

```bash
drush user-create your_user_name --mail="<your_user_email@email.com>" --password="your user password"
```

**Assign the role administrator to your user:**

```bash
drush user-add-role "administrator" your_user_name
```
