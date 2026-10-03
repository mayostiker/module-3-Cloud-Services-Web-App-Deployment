# module-3-Cloud-Services-Web-App-Deployment
Cloud Services &amp; Web App Deployment
# Cloud Task App

A cloud-based To-Do web application developed as part of the Cloud Computing & DevOps Internship.

The application demonstrates how a Python Flask web application can be deployed on AWS using EC2, Amazon RDS, Amazon S3, IAM, VPC, Security Groups, Nginx, and Gunicorn.

## Project Overview

The Cloud Task App allows users to add and view tasks through a web interface.

The application uses:

- Python Flask for the web application
- MySQL for persistent task storage
- Amazon EC2 for application hosting
- Amazon RDS for the managed MySQL database
- Amazon S3 for object storage
- IAM Role for EC2-to-S3 access
- Amazon VPC for network isolation
- Security Groups for traffic control
- Nginx as a reverse proxy
- Gunicorn as the Flask application server
- CloudWatch for monitoring and log collection

## Architecture

```text
                    Internet
                       |
                       |
                 HTTP Port 80
                       |
                       v
              +----------------+
              |   Amazon EC2   |
              |                |
              |     Nginx      |
              |       |        |
              |       v        |
              |   Gunicorn     |
              |       |        |
              |       v        |
              |  Flask App     |
              +-------+--------+
                      |
             +--------+--------+
             |                 |
             v                 v
       Amazon RDS           Amazon S3
          MySQL             Object Storage

AWS Infrastructure
Amazon VPC

A custom VPC was created for the project:

VPC CIDR: 10.0.0.0/16
Public subnet: 10.0.1.0/24
Private DB subnet 1: 10.0.2.0/24
Private DB subnet 2: 10.0.3.0/24

The EC2 web server is deployed in the public subnet, while Amazon RDS is deployed in private subnets.

EC2

An Amazon Linux 2023 EC2 instance was used to host the application.

The EC2 instance runs:

Nginx
Gunicorn
Flask
Python
MySQL Connector

Nginx receives HTTP requests on port 80 and forwards them to Gunicorn running locally on port 8000.

Port 8000 is not exposed publicly.

Amazon RDS

Amazon RDS for MySQL was used as the application's persistent database.

The database contains a tasks table with the following fields:

id
title
completed
created_at

The RDS instance is not publicly accessible.

Database access is restricted to the EC2 security group.

Amazon S3

An S3 bucket was created for object storage.

The bucket is private and Block Public Access is enabled.

An EC2 IAM role provides the application server with permission to retrieve objects from the S3 bucket.

IAM

An IAM role was attached to the EC2 instance to provide AWS service access without storing AWS access keys on the server.

The role provides controlled access to the project's S3 bucket.

Security Groups
EC2 Security Group

Inbound traffic:

SSH (22): restricted to the administrator's IP address
HTTP (80): allowed for the web application
RDS Security Group

Inbound traffic:

MySQL (3306): allowed only from the EC2 security group

This prevents direct public access to the database.

Environment Variables

Database configuration is supplied through environment variables instead of hard-coding credentials in the application source code.

The application uses:

DB_HOST
DB_PORT
DB_USER
DB_PASSWORD
DB_NAME

Sensitive credentials are not stored in the GitHub repository.

Application

The application was developed using Flask.

The main application functionality includes:

Displaying existing tasks
Adding new tasks
Storing tasks in Amazon RDS MySQL
Retrieving tasks from the database
Deployment Flow
User
  |
  v
Nginx
  |
  v
Gunicorn
  |
  v
Flask Application
  |
  +----> Amazon RDS MySQL
  |
  +----> Amazon S3
Testing

The following tests were performed:

EC2 to RDS connectivity test
MySQL database connection test
Flask application test
Gunicorn application server test
Nginx reverse proxy test
EC2 to S3 object retrieval test
Public web application test
Database persistence test
Live Application

Live Website:

http://100.59.48.98

Technologies Used
AWS
Amazon EC2
Amazon RDS
Amazon S3
Amazon VPC
IAM
Security Groups
Nginx
Gunicorn
Python
Flask
MySQL
Linux
Learning Outcomes

This project provided practical experience with:

Deploying applications on AWS
Designing a basic AWS network architecture
Working with EC2 and RDS
Configuring Security Groups
Managing IAM permissions
Connecting an application to a managed database
Using environment variables for configuration
Configuring Nginx as a reverse proxy
Running Flask with Gunicorn
Using Amazon S3 for object storage
Testing connectivity between AWS services
Monitoring and logging with CloudWatch

## Linux Commands Used During Deployment

The following Linux commands were used during the deployment and configuration of the Cloud Task App on Amazon EC2.

### 1. Navigation and File Management

```bash
# Show current directory
pwd

# List files
ls

# List all files with details
ls -la

# Change directory
cd cloud-task-app

# Move to the parent directory
cd ..

# Return to the home directory
cd ~

# Create a directory
mkdir cloud-task-app

# Create directories including parent directories
mkdir -p /opt/aws/amazon-cloudwatch-agent/etc

# Create/edit application files
nano app.py
nano database.py
nano requirements.txt

# Edit HTML and CSS files
nano templates/index.html
nano static/style.css

# Edit Nginx configuration
sudo nano /etc/nginx/nginx.conf

# Create/edit CloudWatch Agent configuration
sudo nano /opt/aws/amazon-cloudwatch-agent/etc/cloudwatch-agent.json

# Install Nginx
sudo dnf install nginx -y

# Install CloudWatch Agent
sudo dnf install amazon-cloudwatch-agent -y

# Create Python virtual environment
python3 -m venv venv

# Activate virtual environment
source venv/bin/activate

# Install application dependencies
pip install -r requirements.txt

# Check Python version
python3 --version

# List installed Python packages
pip list

Environment Variables

Database credentials were provided through environment variables instead of being hard-coded into the application.

export DB_HOST="module3-mysql.czwycqois2o1.us-east-1.rds.amazonaws.com"
export DB_PORT="3306"
export DB_USER="taskadmin"
export DB_NAME="taskapp"

# Enter the database password securely
read -s DB_PASSWORD

# Export the password as an environment variable
export DB_PASSWORD

Testing RDS Connectivity

The connection between the EC2 instance and Amazon RDS was tested using Netcat.

nc -zv module3-mysql.czwycqois2o1.us-east-1.rds.amazonaws.com 3306

This verified that the EC2 instance could reach the MySQL database on port 3306.

Connecting to MySQL
mysql -h module3-mysql.czwycqois2o1.us-east-1.rds.amazonaws.com \
-P 3306 \
-u taskadmin \
-p

Useful MySQL commands:

SHOW DATABASES;
USE taskapp;
SHOW TABLES;
SELECT * FROM tasks;
exit;

Running and Testing Flask

The Flask application was initially tested using the Flask development server.

python3 app.py

Test the application locally:

curl http://127.0.0.1:5000

Test adding a task:

curl -X POST \
-d "task=Learn AWS" \
http://127.0.0.1:5000/add

Running Gunicorn

Gunicorn was used as the application server.

gunicorn --bind 127.0.0.1:8000 app:app
Test Gunicorn locally:
curl http://127.0.0.1:8000

The application server was bound to 127.0.0.1:8000 so that it was not directly exposed to the Internet.

Nginx Configuration and Testing

Check the Nginx version:
nginx -v

Test the Nginx configuration:
sudo nginx -t

Start Nginx:
sudo systemctl start nginx

Enable Nginx to start automatically:
sudo systemctl enable nginx

Reload Nginx after configuration changes:
sudo systemctl reload nginx

Check Nginx status:
sudo systemctl status nginx

Test the Nginx reverse proxy:
curl http://127.0.0.1

The deployment flow was:

Internet
   |
   v
Nginx :80
   |
   v
Gunicorn :8000
   |
   v
Flask Application
   |
   +----> Amazon RDS MySQL
   |
   +----> Amazon S3

Process Management and Troubleshooting

Check running Gunicorn processes:
ps aux | grep gunicorn

Stop a process:
kill PID

Forcefully terminate a process when necessary:
kill -9 PID

Systemd Service Management

The application was configured for a systemd service.

# Reload systemd configuration
sudo systemctl daemon-reload

# Enable application service
sudo systemctl enable cloud-task-app

# Start application service
sudo systemctl start cloud-task-app

# Check service status
sudo systemctl status cloud-task-app

# View application service logs
sudo journalctl -u cloud-task-app

Monitoring Logs

Monitor Nginx access logs:
sudo tail -f /var/log/nginx/access.log

Monitor Nginx error logs:
sudo tail -f /var/log/nginx/error.log

View systemd application logs:
sudo journalctl -u cloud-task-app

AWS CLI Commands

Check the AWS identity associated with the EC2 IAM role:
aws sts get-caller-identity

Retrieve an object from Amazon S3:
aws s3api get-object \
--bucket codomax-module3-mayowa-2026 \
--key module3-test.txt \
module3-test-downloaded.txt

Display the downloaded S3 object:
cat module3-test-downloaded.txt

CloudWatch Agent

Create the CloudWatch configuration directory:
sudo mkdir -p /opt/aws/amazon-cloudwatch-agent/etc

Create the CloudWatch Agent configuration file:
sudo nano /opt/aws/amazon-cloudwatch-agent/etc/cloudwatch-agent.json

Install the CloudWatch Agent:
sudo dnf install amazon-cloudwatch-agent -y

Start the CloudWatch Agent using the configuration file:
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
-a fetch-config \
-m ec2 \
-c file:/opt/aws/amazon-cloudwatch-agent/etc/cloudwatch-agent.json \
-s

Check the CloudWatch Agent status:
sudo systemctl status amazon-cloudwatch-agent

Important Commands Summary
Command     	                              Purpose
pwd	                                        Show current directory
ls -la	                                    List files and details
cd	                                        Change directory
mkdir	                                      Create directory
nano	                                      Create/edit files
cat	                                        Display file contents
dnf install	                                Install Linux packages
python3 -m venv	                            Create Python virtual environment
source	                                    Activate virtual environment
export	                                    Set environment variables
nc -zv	                                    Test network port connectivity
mysql	                                      Connect to MySQL
curl	                                      Test HTTP endpoints
gunicorn	                                  Run the Flask application server
nginx -t	                                  Test Nginx configuration
systemctl	                                  Manage Linux services
journalctl	                                View systemd logs
ps aux	                                    View running processes
kill	                                      Stop a process
tail -f	                                    Monitor log files
aws sts get-caller-identity	                Check AWS IAM identity
aws s3api get-object	                      Retrieve an S3 object
