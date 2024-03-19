# CloudWatch Monitoring and SNS Alert Configuration

## Step 1: Navigate to CloudWatch

- Find and select **CloudWatch** under the **Management & Governance** category.

## Step 2: Create a Dashboard

- Click on **Dashboards** in the left navigation pane.
- Click **Create dashboard**.
- Name your dashboard `EC2MonitoringDashboard` and click **Create dashboard**.

## Step 3: Add Metrics to Your Dashboard

- Click **Add widget**.
- Choose the type of widget and click **Configure**. For EC2 metrics, select the **EC2** namespace, then **Per-Instance Metrics**.
- Choose the instance and metric you want to monitor. For this project, include all three instances per metric in one widget (since it’s easy to compare).
- After selecting the metrics, click **Create widget**.
- Specifically, monitor the **CPUUtilization**, **DiskReadBytes**, and **NetworkIn** metrics from the natively provided metrics in CloudWatch.
- Repeat this process to add additional metrics for the EC2 instances to the dashboard.

## Step 4: Create Alarms for Critical Metrics

- Navigate to **Alarms** in the left navigation pane and click **Create alarm**.
- Click on **Select metric**, choose **EC2** -> **Per-Instance Metrics**, and select the metric you want to create an alarm for.
- Click **Select metric** to proceed.
- Configure the alarm condition:
  - Specify the threshold type as Static.
  - Set the condition (e.g., Greater/Equal, Lower/Equal) and the threshold value based on your monitoring needs (e.g., CPU Utilization >= 80% for high CPU usage).
- In **Notification**, choose **In alarm** and create a new one to notify when the alarm state is reached. You’ll need an email address for notifications if you’re setting up a new SNS topic. Make sure that it is confirmed by going to the email account.
- Click **Create alarm**.
- Repeat the process for other critical metrics.

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

## Resources

- [How to setup AWS CloudWatch's agent on Ubuntu for custom metrics](https://stackoverflow.com/questions/62242033/how-to-setup-aws-cloudwatchs-agent-at-ubuntu-to-get-correct-custom-metrics-li)
