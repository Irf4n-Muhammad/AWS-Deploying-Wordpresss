# AWS Deploying Wordpress

## 1. Project Description:
We will try to interate the wordpress with the AWS and launch the wordpress publicly by leveraging some AWS services so it would be easy to manage.

## 2. Pipeline Architecture Design:
![Pipeline Architecture](image/aws-pipeline.png)

### The explanation:
   - EC2 Write: Write the content
   - EC2 Read: Read the content from two different bucket (One from the main content and media for image or videos)
   - Cloudfront would help to reduce the cost by deploy it to the edge, so it can be accessed in the closer AWS region by the user
   - Application Load Balancer would help to manage the user to get the healthy ec2 read instance.
   - We have to database master (as the main database) and standby as the backup if there is a problem. They will be put in the different AZ (Availability Zone) so we can use it as disaster recovery
   - Auto Scaling group would help to manage the number of EC2 following the needs (it could be shrink down or shrink up depends on the demands)

## 3. Procedure:
### 3.1. Create 2 S3 bucket with name "rlp-code-aws" and "rlp-media-aws" (You can change it, but it must be unique globally). Retain the default settings.

<img width="825" alt="image" src="https://github.com/Irf4n-Muhammad/AWS-Deploying-Wordpresss/assets/121205860/510b6617-2665-4956-96c1-0d4d982c8226">

### 3.2. Create a new cloudfront distribution and select the origin domain name as the media s3 bucket "rlp-media-aws". Leave all the default setting.

<img width="853" alt="image" src="https://github.com/Irf4n-Muhammad/AWS-Deploying-Wordpresss/assets/121205860/d7e67bd7-c516-412d-a928-5f51b263df30">

### 3.3. Navigate to the VPC console and create the security groups:
   - WebDMZ
     -  Inbound - HTTP - 80 - Source - all IP
     -  Inbound - SSH -22 - Source - All IP
     -  Outbound - All - All - All IP

<img width="836" alt="image" src="https://github.com/Irf4n-Muhammad/AWS-Deploying-Wordpresss/assets/121205860/43a453a2-9486-4c7b-a259-0991c884ceed">

   - RDSMySQL
     - Inbound - MySQL/Aurora - 3306 - WebDMZ security group
     - Outbound - All - All - All IP

<img width="840" alt="image" src="https://github.com/Irf4n-Muhammad/AWS-Deploying-Wordpresss/assets/121205860/db328ffc-5f1f-4b26-94cc-25643357d10a">

### 3.4. Navigate to RDS console and create MySQL as database template as production:
   - DB instance identifier - rpllab1
   - Master username - rpllab
   - Password - ******
   - Select burstable class and select "Include previous generation class"ty
   - Select storage type as General Purpose SSD and not provisioned IOPS
   - DB instance size - dbt.t2.micro
   - Disable storage autoscaling

<img width="648" alt="image" src="https://github.com/Irf4n-Muhammad/AWS-Deploying-Wordpresss/assets/121205860/eb9c9ac0-eea5-470c-99fe-0061e5c68549">

### 3.5. Create the IAM role
   Create the new AMI and attach the IAM role and attach the policies "AWSS3FullAccess" and create the new role with name "S3AllAccess"

<img width="650" alt="image" src="https://github.com/Irf4n-Muhammad/AWS-Deploying-Wordpresss/assets/121205860/8751d3cc-68f9-4d6e-9265-df45d1834099">

### 3.7 SSH into the EC2 and launched the code
Run this command
```bash
sudo yum update -y
sudo yum install -y httpd
sudo service httpd start
sudo amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
cd wordpress
cp wp-config-sample.php wp-config.php
cd /home/ec2-user
sudo cp -r wordpress/* /var/www/html/
sudo su
cd /var/www/html/
echo "healthy" > healthy.html
wget https://s3.amazonaws.com/bucketforwordpresslab-dontdelete/htaccess.txt
mv htaccess.txt .htaccess
chkconfig httpd on
service httpd restart
sudo yum install -y mysql
export MYSQL_HOST=<your-RDS-endpoint>
```

### 3.8 Login to the MySQL RDS Database

```bash
mysql --user=rpllab --password=password rpllab
```

### 3.9 Once logged into MySQL and then execute
```bash
CREATE USER 'wordpress' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON rpllab.* TO 'wordpress';
FLUSH PRIVILEGES;
EXIT;
```

### 3.10 Edit wp-config.php in /var/www/html and modify it
```bash
define( 'DB_NAME', 'rpllab' );

/** MySQL database username */
define( 'DB_USER', 'wordpress' );

/** MySQL database password */
define( 'DB_PASSWORD', 'password' );

/** MySQL hostname */
define( 'DB_HOST', 'your-RDS-endpoint' );
```

### 3.11

