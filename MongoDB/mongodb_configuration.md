# Mongo DB configuration

## Install

**<https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/>**

### MongoDB connection string 

<https://docs.mongodb.com/guides/server/drivers/>:

```bash
mongo mongodb://$[hostlist]/$[database]?authSource=$[authSource] --username $[username]
```

### Authentification in MongoDB

- <https://docs.mongodb.com/manual/core/authentication/>
- <https://docs.mongodb.com/manual/tutorial/configure-scram-client-authentication/>

### Update configuration /etc/mongod.conf

<https://www.mongodb.com/basics/create-database>

### TLS

- Nginx as TLS terminator <https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-tcp/>
- Mongo SSL config <https://docs.mongodb.com/manual/tutorial/configure-ssl/>

### Commands

- Create superAdmin user

    ```bash
    # First choose database, then create user
    mongosh --port 27017
    use admin   
    db.createUser(
    {
        user: "myUserAdmin",
        pwd: passwordPrompt(), // or cleartext password
        roles: [
        { role: "userAdminAnyDatabase", db: "admin" },
        { role: "readWriteAnyDatabase", db: "admin" }
        ]
    }
    )
    ```

- Create database user

    ```bash
    use admin
    
    db.createUser({
    user: "testUser",
    pwd: "password",
    roles: [
        { role: "readWrite", db: "<your_database>" }
    ]
    })
    ```

- Change user password:

    ```bash
    use admin
    db.changeUserPassword("admin", passwordPrompt())
    ```

- Create MongoDB dump

    ```bash
    mongodump --collection=<collection> --db=<database> --authenticationDatabase=admin --username=<user>--password="<password>" --out=backup/
    ```

- Restore specific MongoDB collection from dump

    ```bash
    mongorestore --authenticationDatabase=admin --username=<user> --password="<password>" --db=<database> --collection=<collection> backup/products.bson
    ```

- Connect to MongoDB:

    ```bash
    mongosh --authenticationDatabase=admin --username=<user> --password="<password>"
    ```

- Number of records in collection

    ```bash
    db.<collection>.find().count()
    ```

- Create indexes:

    ```bash
    db.products.createIndex({product_name: "text", brand: "text", short_description: "text", long_description: "text"})
    ```

- Remove collection:

    ```bash
    db.<collection>.drop()
    ```

- Check index:

    ```bash
    db.<collection>.getIndexes()
    ```

- Add roles to user

    ```bash
    db.grantRolesToUser( "user", ["clusterMonitor"])
    ```
