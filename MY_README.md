# Steps
### 1. create an S3 Bucket and set permissions
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

### 2. create RDS instance
* make it publicly available to connect to it from outside
* connect via: `psql -h udagram.c0cjyfnrsyhu.eu-central-1.rds.amazonaws.com -U postgres postgres`
* inside postgres:
  * list all databases: `\l`
  * change current database: `\c postgres`
  * list tables, if any: `\dt`
* exit with: `\quit`
* **???** `CIDR/IP - Inbound rule for 0.0.0.0/0 do I need this when the instance is already publicly available`

### 3. set the environment variables
They allow to connect to S3 and RDS (either from the localhost or from withint the AWS)

### 4. create Dockerfile for the frontend application

Backend APIs links (after the respective node apps have been started):
* http://localhost:8080/api/v0/users/flore.vlad@gmail.com
* http://localhost:8081/api/v0/feed

# Commands
* `cp -r ../udagram-api/. .`
* `grep --exclude-dir=node_modules -nrw . -e mock`
* `docker build -t udagram-frontend .`
* `docker run --rm -d --name udagram-frontend -p 8080:80 udagram-frontend:latest`
* `docker container stop udagram-frontend`
* `docker exec -it <container-id> /bin/sh`
* `pwd | xclip -selection clipboard`
* `docker images | grep none | tr -s ' ' | cut -d ' ' -f 3 | xargs docker image rm -f`
* `docker tag udagram-frontend:latest vladflore/udagram-frontend:latest`
* `docker inspect <image-id>`
* `docker run --rm -d --name udagram-users-api --env-file ../env -p 8080:80 udagram-users-api:latest`
* `docker container ls -a | tr -s ' ' | cut -d ' ' -f 1 | xargs docker container rm`