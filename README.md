#![Alt text](/dynamic-web-app-ref-ach3.png)

#![Alt text](/dynamic-web-app-ref-ach2.png)

#![Alt text](/dynamic-web-ref-ach.png)

#![Alt text](/3._Host_a_Dynamic_Web_App_on_AWS.png)


# Dynamic Website Hosting on AWS

## Project Overview
This project demonstrates hosting a dynamic website on AWS, leveraging various AWS resources to ensure reliability, scalability, security, and cost-effectiveness. The infrastructure was provisioned using best practices and a GitHub repository containing the reference diagram and deployment scripts.

## Features and Architecture

### AWS Resources Used:

1. **Virtual Private Cloud (VPC):** Configured with both public and private subnets across two availability zones to ensure high availability and fault tolerance.
2. **Internet Gateway:** Enabled connectivity between VPC instances and the wider internet.
3. **Security Groups:** Configured as network firewalls to control inbound and outbound traffic.
4. **Availability Zones:** Leveraged two zones for increased reliability and fault tolerance.
5. **Public Subnets:** Utilized for components such as the NAT Gateway and Application Load Balancer.
6. **EC2 Instance Connect Endpoint:** Enabled secure connections to resources in public and private subnets.
7. **Private Subnets:** Hosted web servers (EC2 instances) to enhance security.
8. **NAT Gateway:** Allowed private subnet instances to access the internet securely.
9. **EC2 Instances:** Hosted the website within the private subnets.
10. **Application Load Balancer:** Distributed web traffic evenly across an Auto Scaling Group of EC2 instances.
11. **Auto Scaling Group:** Managed EC2 instances for scalability, fault tolerance, and elasticity.
12. **AWS Certificate Manager:** Secured communications with SSL/TLS certificates.
13. **Simple Notification Service (SNS):** Configured to provide alerts on Auto Scaling Group activities.
14. **Route 53:** Used for domain name registration and DNS record setup.
15. **S3:** Stored application code.
16. **RDS:** Hosted the dynamic web application database.
17. **Amazon Machine Image (AMI):** Utilized for consistent EC2 instance setups.

## Deployment Instructions

### Deployment Script
The deployment script automates the installation and configuration of necessary software on the EC2 instances:

```bash
#!/bin/bash

# Update server packages
sudo yum update -y

# Install Apache web server
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# Install PHP and necessary extensions
sudo dnf install -y \
php \
php-pdo \
php-openssl \
php-mbstring \
php-exif \
php-fileinfo \
php-xml \
php-ctype \
php-json \
php-tokenizer \
php-curl \
php-cli \
php-fpm \
php-mysqlnd \
php-bcmath \
php-gd \
php-cgi \
php-gettext \
php-intl \
php-zip

# Install MySQL 8
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
dnf repolist enabled | grep "mysql.*-community.*"
sudo dnf install -y mysql-community-server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Enable mod_rewrite in Apache
sudo sed -i '/<Directory "/var/www/html">/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/httpd/conf/httpd.conf

# Define S3 bucket name
S3_BUCKET_NAME=saudat-project-web-files

# Sync application code from S3
sudo aws s3 sync s3://"$S3_BUCKET_NAME" /var/www/html

# Extract and deploy application code
cd /var/www/html
sudo unzip shopwise.zip
sudo cp -R shopwise/. /var/www/html/
sudo rm -rf shopwise shopwise.zip

# Set permissions
sudo chmod -R 777 /var/www/html
sudo chmod -R 777 storage/

# Edit environment file
sudo vi .env

# Restart Apache server
sudo service httpd restart
```

## Usage

1. **Setup:**
   - Clone the GitHub repository.
   - Use the provided reference diagram for architecture insights.
   - Deploy the resources using AWS Management Console or IaC tools.

2. **Deployment:**
   - Run the deployment script on the EC2 instances.
   - Ensure proper IAM roles are assigned to allow S3 access and other necessary permissions.

3. **Monitoring:**
   - Use CloudWatch for monitoring resource performance.
   - Configure SNS for alerts related to Auto Scaling Group activities.

4. **Maintenance:**
   - Regularly update EC2 instances.
   - Monitor database performance and scale RDS if required.

## Key Highlights

- **Scalability:** Auto Scaling Group ensures that the application scales dynamically based on demand.
- **Security:** Private subnets and security groups provide robust security for application resources.
- **Reliability:** Multi-AZ deployment ensures high availability and fault tolerance.
- **Cost-Effectiveness:** Leveraging AWS’s managed services reduces overhead and operational costs.





