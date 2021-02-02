# Steps
1. create an S3 Bucket and set permissions
* create the bucket policy:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1612276008276",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:DeleteObject",
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::udagram-764533981594-dev/*"
        },
        {
            "Sid": "MyListBucketStmt",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::udagram-764533981594-dev"
        }
    ]
}
```
Note: Actions related to Objects require `Resource` to end with `/*` for the respective bucket, e.g. `arn:aws:s3:::udagram-764533981594-dev/*`. The `ListBucket` action works at the bucket level, rather than the objects level, reason why it has to be specified in a new statement.

* create the CORS configuration:
```json
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "PUT",
            "POST",
            "DELETE"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": []
    }
]
```

2. create RDS instance
* make it publicly available to connect to it from outside
* connect via: `psql -h udagram.c0cjyfnrsyhu.eu-central-1.rds.amazonaws.com -U postgres postgres`
* inside postgres:
  * list all databases: `\l`
  * change current database: `\c postgres`
  * list tables, if any: `\dt`
* exit with: `\quit`
* **???** `CIDR/IP - Inbound rule for 0.0.0.0/0 do I need this when the instance is already publicly available`

# Commands
* `cp -r ../udagram-api/. .`
* 