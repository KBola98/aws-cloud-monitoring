# Creating a Lambda Function to Generate Weekly Summary Reports from Metrics Stored in DynamoDB

This section guides you through the process of setting up a Lambda function designed to generate weekly summary reports based on metrics stored in a DynamoDB table.

## Step 1: Prepare DynamoDB Table

Your DynamoDB table needs to be properly set up to store the metrics you plan to report on. Ensure it includes a timestamp or date attribute to enable querying data from the last 7 days.

1. **Navigate to DynamoDB**:
   - In the AWS Management Console, find DynamoDB under Services.

2. **Create a New Table**:
   - **Table name**: Choose a name, I'll be using MetricsTable1".
     ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/d53b78cf-26a3-451e-9802-175ffe43dfb6)
     
   - **Primary key**: Use a composite key where the partition key could be `InstanceId` and the sort key `Timestamp`.
     ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/2ef94797-17ec-4037-9b89-de07b9478017)


## Step 2: Create the Lambda Function

1. **Navigate to Lambda**:
   - In the AWS Management Console, find Lambda under Services and click "Create function".
     ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/c1bdb220-416e-4cf0-af80-66462a812c4a)


2. **Select "Author from scratch"**:
   - **Function name**: Give it a descriptive name, e.g., "WeeklySummaryReports".
   - **Runtime**: Select Python.
   - **Permissions**: Choose or create an execution role that has permissions to access CloudWatch and DynamoDB.
     ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/52bc6ed7-e623-4afd-bfb1-95b86e9fd24e)
     ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/52580119-4a38-472a-9422-71f2e033a9d4)



3. **Function Code**:
   - Your Lambda function will collect metrics such as CPUUtilization, format the data into a JSON document, and insert the JSON document into the DynamoDB table you created.(Copy and paste it for Lambda_function section)
     ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/99abcbcd-4240-476d-a7ae-916e6740b2c4)

 ( You need to replace 'YourDynamoDBTableName' with the actual name of your DynamoDB table.)  
```python
import boto3
from datetime import datetime

# Initialize the Boto3 clients
cloudwatch = boto3.client('cloudwatch')
dynamodb = boto3.resource('dynamodb')

def lambda_handler(event, context):
    # Define the metric you want to retrieve
    metric_name = 'CPUUtilization'
    namespace = 'AWS/EC2'
    statistic = 'Average'  # You could use 'Sum', 'Maximum', etc.
    
    # Specify the time range for the metric
    # For simplicity, this example gets the metric for the last 5 minutes.
    end_time = datetime.now()
    start_time = end_time - timedelta(minutes=5)
    
    # Define which EC2 instance you're interested in
    instance_id = 'YOUR_INSTANCE_ID'
    dimensions = [{'Name': 'InstanceId', 'Value': instance_id}]
    
    # Retrieve the metric from CloudWatch
    response = cloudwatch.get_metric_data(
        MetricDataQueries=[
            {
                'Id': 'm1',
                'MetricStat': {
                    'Metric': {
                        'Namespace': namespace,
                        'MetricName': metric_name,
                        'Dimensions': dimensions
                    },
                    'Period': 300,  # 5 minutes in seconds
                    'Stat': statistic,
                },
                'ReturnData': True,
            },
        ],
        StartTime=start_time,
        EndTime=end_time,
    )
    
    # Extract the value from the response (simplified for clarity)
    metric_value = response['MetricDataResults'][0]['Values'][0] if response['MetricDataResults'][0]['Values'] else 0
    
    # Prepare the data for DynamoDB
    table = dynamodb.Table('YourDynamoDBTableName')
    item = {
        'InstanceId': instance_id,
        'Timestamp': datetime.now().strftime('%Y-%m-%dT%H:%M:%S'),
        'CPUUtilization': metric_value,
    }
    
    # Insert the data into DynamoDB
    table.put_item(Item=item)
    
    return {
        'statusCode': 200,
        'body': f"Successfully inserted metric data for instance {instance_id}"
    }

