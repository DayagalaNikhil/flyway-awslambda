Lambda function for AWS RDS Migration using Flyway.
====
 
**Now developing...**

# Setup

## Create S3 bucket

Put your Flyway resources to S3 bucket.
 
### Bucket structure

```
s3://my-flyway
  - /my-application
    - flyway.conf          <- Flyway configuration file.
    - V1__create_foo.sql   <- SQL file(s)
    - V2__create_bar.sql
```

## Deploy Lambda function

### Code

* Build lambda function module.
```
sbt assembly
```

* Upload `target/scala-x.x.x/flywayAwsLambda-assembly-x.x.x.jar`.

### Configuration

||value|
|----|----|
|Runtime|`Java 8`|
|Handler|`crossroad0201.aws.flywaylambda.S3EventMigrationHandler::handleRequest`|
|Role|See `Role` section.|
|Timeout|`5 min.`|
|VPC|Same VPC as target RDS.|

#### Role

Require policies.

* AmazonRDSFullAccess
* AmazonS3FullAccess
* AmazonLambdaVPCAccessExecutionRole

### Triggers

Add trigger `S3 to Lambda`.

||value|Example|
|----|----|----|
|Bucket|Your Flyway migration bucket.|`my-flyway`|
|Event type|`Object created`|-|
|Prefix|Your Flyway migration files location.|`my-application`|
|Suffix|`sql`|-|


# Run

Put Flyway SQL file to S3 bucket.

Invoke flyway-lambda automatically by S3 event.
