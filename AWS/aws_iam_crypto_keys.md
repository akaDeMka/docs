# AWS IAM Crypto Keys

Policy allow use custom Crypto Key:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "KMSKeypermission",
            "Effect": "Allow",
            "Action": [
                "kms:Encrypt",
                "kms:Decrypt",
                "kms:ReEncrypt*",
                "kms:GenerateDataKey*",
                "kms:DescribeKey"
            ],
            "Resource": [
                "<crypto_key_arn>"
            ]
        }
    ]
}
```
