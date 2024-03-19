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
   - Once the function is created, you'll be taken to the function's configuration page.
   - Scroll to the "Code" section. Here, you'll see an online code editor.
   - Copy the Python code provided in the previous message and paste it into the online code editor. Make sure to replace placeholders like 'YOUR_INSTANCE_ID' and 'YourDynamoDBTableName' with 
     actual values relevant to your AWS environment.


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
```



## Step 5: Deploy and Test

- **Save and Deploy**: Back in the Lambda function editor, click "Deploy" to save your code.
- **Test**: Click the "Test" button to create a new test event. You don't need to modify the default template for the test event because the function doesn't use event input. Name your test event and click "Save changes", then "Test" to execute your function.
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/2fe5149f-728b-493c-9234-306e99d76338)

- **Verify**: Check your DynamoDB table to see if the new entry was added. Also, review the execution logs in the Lambda console for any errors or success messages.
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/3e5fe6ad-8bc1-406a-b182-610b83117e16)
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/0cebc244-d4e0-4cbf-a1bf-ccb862120b2f)


## Step 6: Schedule Execution of the Lambda function to collect 1 min Metric data

To run your Lambda function automatically at a defined interval (e.g., every minute):

- **Amazon EventBridge**: Go to the Amazon EventBridge service in the AWS Management Console.
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/888fc69d-4424-4790-8d2f-5f4d785afb56)
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/83630701-08ac-47ad-9f9a-cd0b13e671d7)


- **Create Rule**: Click "Create rule", set a name and description. For "Define pattern", select "Schedule" and define your desired schedule expression.
  
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/c0d7436e-136d-450b-874e-fbbeeb92f624)
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/754707b4-a9b5-49b3-963f-ccb7543dc952)

- **Select Target**: Choose "Lambda function" as the target and select your Lambda function from the list.
- 
    ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/baddf119-6234-47dc-a7bd-4179aeba03f7)
   
- **Configure Details and Create**: Confirm the details and click "Create rule".
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/cb609542-3124-41b9-8399-e3f1a81f2594)
