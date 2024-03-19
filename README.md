# Cloud Resource Monitoring

## Project Overview

This project sets up a comprehensive monitoring and alerting system for AWS EC2 instances, leveraging AWS CloudWatch, SNS, Lambda, and DynamoDB. It aims to automate infrastructure provisioning with Terraform and configuration with Ansible for scalable cloud resource management.

## Features

1.	Monitoring Setup: Implementation of monitoring for five critical metrics across three AWS EC2 instances. The metrics include:
   •	CPU Utilization
   •	Memory Usage
   •	Disk I/O Operations
   •	Network I/O Operations
   •	Disk Space Usage
2.	Alerting Mechanism: Configuration of threshold-based alerts for monitored metrics to ensure proactive issue detection and resolution.
3.	Data Collection and Storage: A mechanism to collect metrics data at one-minute intervals, formatted into JSON documents and stored in DynamoDB for historical analysis.
4.	Weekly Summary Reports: An automated report generated and emailed weekly, summarizing the high, low, and average values of the monitored metrics.
5.	Automation Framework: Utilization of Terraform for infrastructure provisioning and Ansible for configuration management, enabling scalable and repeatable deployments.
6.	Error Logging and Notification: Implementation of error logging for the automation scripts and monitoring system, with notifications for critical errors.


## Getting Started

Follow the steps outlined in the documentation to set up and configure your monitoring system:

1. [AWS Setup](docs/aws-setup.md) - Setting up AWS services and EC2 instances.
2. [CloudWatch Configuration](docs/cloudwatch-configuration.md) - Configuring monitoring and alarms.
3. [SNS Notifications](docs/sns-notifications.md) - Setting up alert notifications.
4. [Lambda Functions](docs/lambda-functions.md) - Implementing data processing and storage.
5. [Terraform Automation](docs/terraform-automation.md) - Automating infrastructure provisioning.
6. [Ansible Configuration](docs/ansible-configuration.md) - Automating system configuration.


