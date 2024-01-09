# AWS Deploying Wordpress

## 1. Project Description:
We will try to interate the wordpress with the AWS and launch the wordpress publicly by leveraging some AWS services so it would be easy to manage.

## 2. Pipeline Architecture Design:
![Pipeline Architecture](image/aws-pipeline.png)

## 3. Procedure:
### 3.1. Create 2 S3 bucket with name "rlp-code-aws" and "rlp-media-aws". Retain the default settings.
### 3.2. Create a new cloudfront distribution and select the origin domain name as the media s3 bucket "rlp-media-aws". Leave all the default setting.
### 3.3. Navigate to the VPC console and create the security groups:
   - WebDMZ
     -  Inbound - HTTP - 80 - Source - all IP
     -  Inbound - SSH -22 - Source - All IP
     -  Outbound - All - All - All IP
   - RDSMySQL
     - Inbound - MySQL/Aurora - 3306 - WebDMZ security group
     - Outbound - All - All - All IP
### 3.4. Navigate to RDS console and create MySQL as database template as production:
   - DB instance identifier - rpllab1
   - Master username - rpllab
   - Password - ******
   - Select burstable class and select "Include previous generation class"
   - DB instance size - dbt.t2.micro
   - Disable storage autoscaling
### 3.5.
