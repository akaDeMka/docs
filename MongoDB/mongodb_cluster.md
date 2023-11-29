# MongoDB Cluster (Replica Set)

For logging data changes in MongoDB need to activate opLog which available only for clusters.

## Steps

1. Create key file for authorization and place it in folder with right permissions (example):

    ```bash
    sudo mkdir /opt/mongodb
    cd /opt/mongodb
    sudo openssl rand -base64 756 > mongodb.key
    sudo chown -R mongodb:mongodb /opt/mongodb
    sudo chmod 400 /opt/mongodb/mongodb.key
    ```

1. Add key authorization option to MongoDB config file:

    ```bash
    security:
        keyFile: /etc/mongodb/mongodb.key
    ```

1. Create user with or add user permission clusterManager (minimal rights to start cluster init)

    ```bash
    #connect to database first
    mongosh mongodb://<user>:<password>X@localhost:27017
    use admin
    db.grantRolesToUser( "<user>", ["clusterManager"])
    ```

    How to create user see [Mongo DB configuration](https://github.com/akaDeMka/docs/tree/main/MongoDB/mongodb_configuration.md)

1. Add replication cluster option to MongoDB config file

    ```bash
    replication:
    replSetName: "rs0"
    ```

1. Connect to database and initiate cluster

    ```bash
    rs.initiate()
    ```

1. Check cluster is working

    ```bash
    rs.status()
    ```

### Articles

- <https://www.mongodb.com/docs/manual/tutorial/enforce-keyfile-access-control-in-existing-replica-set/>
- <https://www.mongodb.com/docs/manual/reference/built-in-roles/>
- <https://www.mongodb.com/docs/manual/tutorial/deploy-replica-set/>
