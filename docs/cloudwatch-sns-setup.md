# CloudWatch Monitoring and SNS Alert Configuration

## Step 1: Navigate to CloudWatch

- Find and select **CloudWatch** under the **Management & Governance** category.
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/3e97e543-3ffe-405a-ad5e-f2a60b48f72d)


## Step 2: Create a Dashboard

- Click on **Dashboards** in the left navigation pane.
- Click **Create dashboard**.
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/d72534c9-0fc7-4d11-b1d3-3ce5a0929915)


- Name your dashboard (I'll be naming as `EC2MonitoringDashboard`) and click **Create dashboard**.

## Step 3: Add Metrics to Your Dashboard

- Click **Add widget**.
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/b013ecbf-306e-40fd-b621-59a5b7baf5a7)

- Choose the type of widget and click **Configure**. For EC2 metrics, select the **EC2** namespace, then **Per-Instance Metrics**.
- Choose the instance and metric you want to monitor. For this project, include all three instances per metric in one widget (since it’s easy to compare).
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/1b7eb2ef-44e9-412b-b747-9dbb8b57e597)

- After selecting the metrics, click **Create widget**.
- Specifically, monitor the **CPUUtilization**, **DiskReadBytes**, and **NetworkIn** metrics from the natively provided metrics in CloudWatch.
- Repeat this process to add additional metrics for the EC2 instances to the dashboard.
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/dcd16f64-1568-470a-b027-49caaa953fdc)

### Additional Steps for Custom Metrics

Follow these steps to enable additional metrics monitoring through the CloudWatch Agent:

1. `sudo wget https://s3.amazonaws.com/amazoncloudwatch-agent/debian/amd64/latest/amazon-cloudwatch-agent.deb`
2. `sudo dpkg -i -E ./amazon-cloudwatch-agent.deb`
3. Run the CloudWatch Agent configuration wizard: `sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard`
4. Answer the prompts as needed, defaulting to most questions, and specifically:
   - NO for storing config in SSM parameter store.
   - YES for turning on StatsD and CollectD monitoring.
   - NO for existing CloudWatch Log Agent queries.
5. To prevent common errors, create a missing directory and file:
   - `sudo mkdir -p /usr/share/collectd/`
   - `sudo touch /usr/share/collectd/types.db`
6. Start the CloudWatch Agent: `sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json -s`
7. Check the status: `/opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -m ec2 -a status`
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/b6c09bf2-3338-4bbe-95f8-0d1b722ceebb)

   Finally, you can confirm this also by going to the Cloudwatch > All Metric - And you will find a custom metrics section.
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/fa96173e-3990-4c88-9e08-4dcebb649e1d)
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/735ba77c-43ac-4de4-920c-4712f4ebbaaa)




## Step 4: Create Alarms for Critical Metrics

- Navigate to **Alarms** in the left navigation pane and click **Create alarm**.
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/63f33d1c-dfcc-4b30-98ca-aacd77d96938)

- Click on **Select metric**, choose **EC2** -> **Per-Instance Metrics**, and select the metric you want to create an alarm for.
- Click **Select metric** to proceed.
- Configure the alarm condition:
  - Specify the threshold type as Static.
  - Set the condition (e.g., Greater/Equal, Lower/Equal) and the threshold value based on your monitoring needs (e.g., CPU Utilization >= 80% for high CPU usage).
    ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/cd022978-cf20-442b-b49d-7ba191943a4c)

- In **Notification**, choose **In alarm** and create a new one to notify when the alarm state is reached. You’ll need an email address for notifications if you’re setting up a new SNS topic. Make sure that it is confirmed by going to the email account.
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/fb4da59e-0b71-4ab6-b81a-85a377313e04)

- Click **Create alarm**.
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/8c54779e-fc98-44b7-bb6f-ec57f3352670)

- Repeat the process for other critical metrics.

  You will get Email notifications as below when it hits the threshold we defined.
  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/4616f8a8-abfa-48c7-9c2d-e6f09d5ebd10)

  ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/b6589ef7-555b-45e1-b142-a588d12115e5)



## Resources

- [How to setup AWS CloudWatch's agent on Ubuntu for custom metrics](https://stackoverflow.com/questions/62242033/how-to-setup-aws-cloudwatchs-agent-at-ubuntu-to-get-correct-custom-metrics-li)
