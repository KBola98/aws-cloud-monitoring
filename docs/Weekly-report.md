# Sending Weekly Summary Emails with AWS Lambda and SES

This section covers how to implement a system for sending weekly summary emails containing high, low, and average metric values stored in DynamoDB, using AWS Lambda and AWS SES.

## Overview

The goal is to automatically query aggregated metrics data from DynamoDB and send a formatted summary via email every week. This involves creating a Lambda function to process the data and using Amazon SES to handle the email delivery.

## Step 1: Aggregate Metrics Data in Lambda

### a. Preparing Your Environment

- Ensure your AWS IAM role for Lambda has permissions for accessing DynamoDB data and sending emails via SES.
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/bb178ccb-511c-4d35-a878-7d1113e02e81)


### b. Creating the Lambda Function

1. **Navigate to AWS Lambda**: Create a new function named `WeeklyMetricsSummarySender`.
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/7da810c1-8bc7-43be-bf68-d7b597f0edf0)

3. **Configuration**:
   - Select "Author from scratch."
   - Choose a runtime (e.g., Python 3.x).
   - Assign an execution role with the necessary permissions.\
     ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/641cfa11-bfb1-4e3f-b7e8-a1a34bbc8899)


### c. Function Implementation

- The Lambda function should:
  - Query DynamoDB for the last 7 days of metrics.
  - Calculate high, low, and average values.
  - Format these statistics into an HTML table for email content.

```python
import boto3
from datetime import datetime, timedelta

# Initialize the DynamoDB and SES clients
dynamodb = boto3.resource('dynamodb')
ses = boto3.client('ses')

def lambda_handler(event, context):
    table = dynamodb.Table('EC2Metrics')  # Replace with your table name
    
    # Calculate the date range for the last 7 days
    end_date = datetime.now()
    start_date = end_date - timedelta(days=7)
    
    # Query the table for data within the last 7 days (customize as needed)
    response = table.scan(
        FilterExpression=Attr('Timestamp').between(str(start_date), str(end_date))
    )
    
    items = response['Items']
    
    # Calculate your metrics (this will depend on your data structure)
    # For example, calculate average CPUUtilization
    
    # Format the message
    email_body = "<h1>Weekly Metrics Summary</h1>"
    email_body += "<table><tr><th>Metric</th><th>High</th><th>Low</th><th>Average</th></tr>"
    
    # Add rows for each metric
    email_body += "<tr><td>CPU Utilization</td><td>{}</td><td>{}</td><td>{}</td></tr>".format(high, low, avg)
    
    email_body += "</table>"
    
    # Send the email
    ses.send_email(
        Source='your-email@example.com',  # SES verified email
        Destination={'ToAddresses': ['recipient@example.com']},
        Message={
            'Subject': {'Data': 'Weekly Metrics Summary'},
            'Body': {'Html': {'Data': email_body}}
        }
    )
```
## Step 2: Schedule Weekly Execution with Amazon EventBridge

To ensure that the `WeeklyMetricsSummarySender` Lambda function runs automatically on a weekly basis to process and send out the metrics summary, you'll need to set up a scheduled event in Amazon EventBridge.

### Create a New Rule in EventBridge

1. **Navigate to Amazon EventBridge**: Access the Amazon EventBridge service within the AWS Management Console.
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/9e60c383-2d30-429a-aecd-4e1077244609)

   
2. **Create Rule**:
   - Click on the "Create rule" button.
   - I will name the rule as `WeeklyMetricsEmailSender`.
     ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/dfaf9dd5-a48d-48d2-bf2e-2f136a52717f)

   
3. **Schedule**:
   - Choose "Schedule" as the rule type.
   - For the schedule pattern, use a cron expression to specify your desired weekly trigger time. For instance, `cron(0 10 * * ? *)` triggers the function every week at 10 AM UTC. This expression means:
     - `0` minute past the hour
     - `10` hour of the day (10 AM)
     - `*` any day of the month
     - `*` any month
     - `?` any day of the week (used when you don't need to specify a particular day)
      ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/d25cbe53-bbe6-4a0a-bf9f-4482166fbc67)


4. **Set the Target**:
   - As the target, choose "Lambda function" and then select your `WeeklyMetricsSummarySender` Lambda function from the list.
     ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/0e9453b4-416f-42cd-acd4-b9f0d4f88bc2)


5. **Rule Configuration**:
   - Provide any additional details or tags as needed for your rule configuration.
   
6. **Create**: Review your settings, then click "Create" to establish the scheduled event.
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/0505ec0e-70dd-4257-b98e-ef05dacbc822)


By following these steps, your Lambda function will be triggered automatically by Amazon EventBridge according to the schedule you've set, ensuring regular weekly execution without manual intervention.

