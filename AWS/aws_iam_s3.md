# AWS IAM S3

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

## Some rules for bucket

* Make logging:

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

* Allow Cloudfront access:

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

* Allow read access with specific header UserAgent:

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

* Allow public access:

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
