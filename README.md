# Cloud Resource Monitoring

## Project Overview

This project sets up a comprehensive monitoring and alerting system for AWS EC2 instances, leveraging AWS CloudWatch, SNS, Lambda, and DynamoDB. 

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

## Architecture Diagram
![Architecture Diagram](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/6b370523-ba8d-40e9-86c3-79b125788775)




## Getting Started

Follow the steps outlined in the documentation to set up and configure your monitoring system:

1. [AWS Setup](docs/aws-setup.md) - Setting up AWS services and EC2 instances.
2. [CloudWatch Monitoring and SNS Alert Configuration](docs/cloudwatch-sns-setup.md) - Configuring monitoring, alarms (thresholds 60% - Amber, 80% - Red) and SNS notifications for alerting 
3. [Lambda Function to store the metrics](docs/lambda-function.md) - Creating a Lambda function Record the 5 Key stats data in a JSON Type Document which can be fed into a DynamoDB and trigger this Lamda Function automatically at every 1 minute using Amazon EventBridge.
4. [Lambda Function for weekly report](docs/Weekly-report.md) -  Sending Weekly Summary Emails with AWS Lambda and SES.


