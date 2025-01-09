# Dynamic Website Deployment on AWS

This project demonstrates hosting a dynamic website on AWS using a highly available and fault-tolerant architecture. Below is an overview of the resources used, the deployment steps, and additional information for successful execution.

---

## **Architecture Overview**
The website is hosted on AWS, utilizing various resources to ensure reliability, security, and scalability. The architecture includes:

1. **Virtual Private Cloud (VPC):** Configured with public and private subnets spanning two availability zones for high availability.
2. **Internet Gateway:** Enabled connectivity between the VPC and the internet.
3. **Security Groups:** Configured as a network firewall for secure access.
4. **Availability Zones:** Leveraged for fault tolerance and reliability.
5. **Public Subnets:** Used for infrastructure components like NAT Gateway and Application Load Balancer.
6. **Private Subnets:** Web servers (EC2 instances) placed here for enhanced security.
7. **NAT Gateway:** Allowed private instances to access the internet securely.
8. **Application Load Balancer (ALB):** Distributed traffic evenly to EC2 instances.
9. **Relational Database Service (RDS):** Hosts the Dynamic web app database.  
10. **Auto Scaling Group (ASG):** Ensured scalability and fault tolerance by managing EC2 instances automatically.
11. **Certificate Manager:** Secured application communication.
12. **Simple Notification Service (SNS):** Configured to provide alerts for activities in the ASG.
13. **Route 53:** Registered a domain name and configured DNS records.
14. **S3 Bucket:** Stored application code for deployment.

---

## **Deployment Steps**
### **1. Bash Script for Configuration**
The provided Bash script automates the deployment of web servers and application components. Below are the steps executed by the script:

```bash
#!/bin/bash

# Update server packages
sudo yum update -y

# Install Apache and enable it
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# Install PHP and required extensions
sudo dnf install -y php php-pdo php-openssl php-mbstring php-exif php-fileinfo php-xml php-ctype php-json php-tokenizer php-curl php-cli php-fpm php-mysqlnd php-bcmath php-gd php-cgi php-gettext php-intl php-zip

# Install MySQL Server
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
dnf repolist enabled | grep "mysql.*-community.*"
sudo dnf install -y mysql-community-server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Enable mod_rewrite for Apache
sudo sed -i '/<Directory "\/var\/www\/html">/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/httpd/conf/httpd.conf

# Sync application code from S3
S3_BUCKET_NAME=saudat-project-web-files
sudo aws s3 sync s3://"$S3_BUCKET_NAME" /var/www/html

# Extract and configure application
cd /var/www/html
sudo unzip shopwise.zip
sudo cp -R shopwise/. /var/www/html/
sudo rm -rf shopwise shopwise.zip

# Set permissions
sudo chmod -R 777 /var/www/html
sudo chmod -R 777 storage/

# Update .env with database credentials
sudo vi .env

# Restart Apache
sudo service httpd restart
```

---

### **2. Key AWS Services Used**
- **EC2 Instances:** Hosted the website with private subnet placement for added security.
- **Auto Scaling Group:** Managed EC2 instance scaling.
- **Application Load Balancer:** Distributed traffic efficiently.
- **NAT Gateway:** Enabled secure internet access for private subnets.
- **S3 Bucket:** Stored application code for deployment.
- **Route 53:** Configured DNS for domain name resolution.
- **SNS:** Sent alerts for scaling events.

---

## **Reference Diagram**
Refer to the GitHub repository for the architecture diagram and deployment scripts: [GitHub Repository](#)



## **Features**
- High availability with multiple availability zones.
- Scalability with Auto Scaling Group.
- Secure architecture leveraging private subnets and NAT Gateway.
- Fault-tolerant load balancing with an Application Load Balancer.
- Secure communication using AWS Certificate Manager.
