# AWS S3 bucket

AWS CLI: </br>
<https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html>
<https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html> </br>
use AWS CLI for downloading files:

```bash
aws s3 sync <folder> s3://<bucket>
#To sync in other side
aws s3 sync s3://<bucket> <folder> 
#Check put rights
aws s3api put-object --bucket <bucket> --key <bucket_file> --body <local_file>
```

## How to integrate S3 to Magento

<https://devdocs.magento.com/guides/v2.4/config-guide/remote-storage/config-remote-storage-aws-s3.html> </br>
<https://aws.amazon.com/premiumsupport/knowledge-center/ec2-instance-access-s3-bucket/></br>
<https://aws.amazon.com/blogs/security/writing-iam-policies-how-to-grant-access-to-an-amazon-s3-bucket/>

```bash
#To enable S3 storage
bin/magento setup:config:set --remote-storage-driver="aws-s3" \
--remote-storage-bucket="<bucket>" --remote-storage-region="<bucket_region>" -n
#To sync with remote storage
bin/magento remote-storage:sync
```

## Nginx

```bash
#Nginx configuration example
  location ~* \.(ico|jpg|jpeg|png|gif|svg|js|css|swf|eot|ttf|otf|woff|woff2)$ {
  # Proxying to AWS S3 storage.
  resolver 8.8.8.8;
  set $bucket "<bucket>.s3.<bucket_region>.amazonaws.com";  
  proxy_pass_request_body off;
  proxy_pass_request_headers off;
  proxy_intercept_errors on;
  proxy_hide_header "x-amz-id-2";
  proxy_hide_header "x-amz-request-id";
  proxy_hide_header "x-amz-storage-class";
  proxy_hide_header "Set-Cookie";
  proxy_ignore_headers "Set-Cookie";
  proxy_pass https://$bucket$uri;
 }
 ```

<https://gist.github.com/josue/11233860> </br>
<https://www.scaleway.com/en/docs/tutorials/setup-nginx-reverse-proxy-s3/>

## S3fs

Working version starts from v1.9 - you have to compile from source code for now. v1.86 is not working. </br>
<https://github.com/s3fs-fuse/s3fs-fuse/blob/master/COMPILATION.md> </br>
<https://github.com/s3fs-fuse/s3fs-fuse>

```bash
#Mount S3fs file system
sudo s3fs "<bucket>:/<bucket_folder>" "<local_path>" \
-o passwd_file=/etc/passwd-s3fs,nonempty,umask=0007,uid=<user_uid>,allow_other
#Unmount
sudo fusermount -u <local_path>
```

#### AWS S3 IAM polices:
Allow rule for main operations with bucket:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "<bucket_arn>"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObjectAcl",
                "s3:GetObject",
                "s3:DeleteObject",
                "s3:PutObjectAcl",
                "s3:ListMultipartUploadParts"
            ],
            "Resource": [
                "<bucket_arn>/*"
            ]
        }
    ]
}
```

#### Some rules for bucket

Make logging:

```json
{
    "Version": "2012-10-17",
    "Id": "S3-Console-Auto-Gen-Policy-1644321283057",
    "Statement": [
        {
            "Sid": "S3PolicyStmt-DO-NOT-MODIFY-1644321282777",
            "Effect": "Allow",
            "Principal": {
                "Service": "logging.s3.amazonaws.com"
            },
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Resource": "<bucket_arn>/*"
        }
    ]
}
```

Allow Cloudfront access:

```json
{
    "Version": "2012-10-17",
    "Id": "S3-Console-Auto-Gen-Policy-1644321283057",
    "Statement": [
        {
            "Sid": "3",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity ETZBGCOJGPEMO"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::hellotera-static/*"
        }
    ]
}
```

Allow read access with specific header UserAgent:

```json
{
 "Version": "2012-10-17",
 "Statement": [
  {
   "Sid": "Allow requests with my secret.",
   "Effect": "Allow",
   "Principal": "*",
   "Action": "s3:GetObject",
   "Resource": "<bucket_arn>/*",
   "Condition": {
    "StringLike": {
     "aws:UserAgent": "<secret_code>"
    }
   }
  }
 ]
}
```

Allow public access:

```json
{
 "Version": "2012-10-17",
 "Id": "S3-Console-Auto-Gen-Policy-1644321283057",
 "Statement": [
  {
   "Sid": "Allow requests to all",
   "Effect": "Allow",
   "Principal": "*",
   "Action": "s3:GetObject",
   "Resource": "<bucket_arn>/*"
  }
 ]
}
```
