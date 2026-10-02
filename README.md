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
