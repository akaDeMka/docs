# Amazon EFS

## EFS creating

1. Create security group for EFS in EC2 console
2. Assignee EFS security group during EFS creation (use bottom “customize”)
3. Add inbound rule: protocol-NFS + EC2 instances security groups from which you want to access.

### Get access to EFS

- Install NFS utilities:

    ```bash
    sudo apt-get install nfs-common
    ```

- Mount EFS to local point:

    ```bash
    sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport <EFS>:/<EFS_folder> /<local_mount_folder>
    ```

- To use with user - create folder with right in EFS share:

    ```bash
    sudo mkdir /<local_mount_folder>/<user_folder>
    sudo chown <user>:<user> /<local_mount_folder>/<user_folder>
    ```

- Mount on startup - add mount point to /etc/fstab:

    ```bash
    <EFS>:/<EFS_folder> /<local_mount_folder> nfs nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport,_netdev 0 0
    ```

    Mount on start up for user:

    ```bash
    <EFS>:/<EFS_folder>/<user_folder> /<local_mount_folder>/<user_folder> nfs nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport,_netdev 0 0
    ```

### Some other commands

```bash
sudo mount -av
aws efs describe-file-systems
```

### Some notes

Magento don’t use software links.

### Articles

- <https://docs.aws.amazon.com/cli/latest/reference/efs/describe-file-systems.html>
- <https://docs.aws.amazon.com/efs/latest/ug/how-it-works.html>
- <https://stackoverflow.com/questions/38632222/aws-efs-connection-timeout-at-mount>
- <https://repost.aws/knowledge-center/datasync-transfer-efs-s3>
- Mounting file systems using the EFS mount helper - Amazon Elastic File System
