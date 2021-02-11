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

### 4. create Dockerfile for the frontend application, /users and /feed apis and reverse-proxy
* add them to travis for CI process

### 5. create deployment and service yaml files
* frontend
* users api
* feed api
* reverse-proxy

### 6. create AWS EKS

### 7. Deploy to AWS EKS
* connect to the udagram-cluster by running command:
  
  `aws eks update-kubeconfig --region eu-central-1 --name udagram-cluster`

  `kubectl config view`
* deploy
 ``` 
 kubectl apply -f udagram-frontend/deployment.yaml 
 kubectl apply -f udagram-frontend/service.yaml 
 kubectl apply -f udagram-users-api/deployment.yaml 
 kubectl apply -f udagram-users-api/service.yaml 
 kubectl apply -f udagram-feed-api/deployment.yaml 
 kubectl apply -f udagram-feed-api/service.yaml 
 kubectl apply -f udagram-reverse-proxy/deployment.yaml 
 kubectl apply -f udagram-reverse-proxy/service.yaml 
```

### 8. Changes after review from Udacity
* Fix the Horizontal Pod Autoscaler
  
  https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/

  https://github.com/kubernetes-sigs/metrics-server#deployment
  
  `kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml`

* Change the type of the endpoints for the frontend and load balancer to load balancer

    see https://knowledge.udacity.com/questions/345075


Backend APIs links (after the respective node apps have been started):
* http://localhost:8080/api/v0/users/flore.vlad@gmail.com
* http://localhost:8081/api/v0/feed

# Commands
* `cp -r ../udagram-api/. .`
* `grep --exclude-dir=node_modules -nrw . -e mock`
* `docker build -t udagram-frontend .`
* `docker run --rm -d --name udagram-frontend -p 8100:80 udagram-frontend:latest`
* `docker container stop udagram-frontend`
* `docker exec -it <container-id> /bin/sh`
* `pwd | xclip -selection clipboard`
* `docker images | grep none | tr -s ' ' | cut -d ' ' -f 3 | xargs docker image rm -f`
* `docker tag udagram-frontend:latest vladflore/udagram-frontend:latest`
* `docker inspect <image-id>`
* `docker run --rm -d --name udagram-users-api --env-file ../env -p 8080:80 udagram-users-api:latest`
* `docker container ls -a | tr -s ' ' | cut -d ' ' -f 1 | xargs docker container rm`
* `kubectl describe pod <pod-name>`
* `kubectl get node -o yaml | grep pods`
* `kubectl exec udagram-feed-deployment-587c6546db-fsgkn -- /bin/bash`
* `kubectl get pod -o wide`
* `kubectl delete -f udagram-feed-api/deployment.yaml`
* `kubectl logs udagram-feed-deployment-587c6546db-q669n`
* `kubectl autoscale deployment udagram-frontend-deployment --cpu-percent=50 --min=1 --max=2`
* `while sleep 0.01; do wget -q -O- http://udagram-feed-service:8080/api/v0/feed; done`
