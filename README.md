# AWS Deploying Wordpress

## Project Description:
We will try to interate the wordpress with the AWS and launch the wordpress publicly by leveraging some AWS services so it would be easy to control.

## Pipeline Architecture Design:
![Pipeline Architecture](image/aws-pipeline.png)

## Procedure:
1. Create 2 S3 bucket with name "rlp-code-aws" and "rlp-media-aws". Retain the default settings.
2. Create a new cloudfront distribution and select the origin domain name as the media s3 bucket "rlp-media-aws". Leave all the default setting.
3. Navigate to the VPC console and create the security groups:
   - WebDMZ
     -  Inbound - HTTP - 80 - Source - all IP
     -  Inbound - SSH -22 - Source - All IP
     -  Outbound - All - All - All IP
   - RDSMySQL
     - Inbound - MySQL/Aurora - 3306 - WebDMZ security group
     - Outbound - All - All - All IP
4. 
