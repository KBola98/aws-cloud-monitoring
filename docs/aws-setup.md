# AWS Environment Setup with EC2 Instances

This guide walks you through the process of setting up your AWS environment and launching EC2 instances.

## Step 1: Log in to the AWS Management Console

1. Navigate to the [AWS Management Console](https://aws.amazon.com/console/).
2. Log in using your AWS account credentials.
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/86a41007-f48f-4724-8b77-0d9db9d4a8ee)

## Step 2: Access the EC2 Dashboard

1. In the AWS Management Console, locate and click on the **Services** menu at the top.
2. Find and select **EC2** under the **Compute** category to open the EC2 Dashboard.
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/a8fb3901-ae19-491b-b876-a14c54bb43b1)


## Step 3: Launch a New EC2 Instance

1. Click on the **Launch Instances** button to start the instance setup process.
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/d7b926cf-2b70-487f-a770-af01764a6a8a)

3. Select Ubuntu as Amazon Machine Image for this project, instance type, and configure your instance settings. You'll also configure a key pair for SSH access to the instance.(Here for this setup I will not use a Key pair)
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/6bdf1e71-6648-4310-a464-3c135e55ea80)
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/d4f59a07-4c2c-4965-b414-8fc18d931bcc)


5. Review your instance configurations. Ensure that everything is set according to your requirements.
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/e321028f-b544-4511-a80b-0d94221bbd2e)

7. Launch your instance.
   ![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/a9b88199-9a42-4e11-b7d5-8273478da570)


## Connecting to Your Instance via SSH

Once the instance is running, you can connect to it using SSH:

1. The AWS Console provides the public DNS or IP address and the connection instructions.
2. Use an SSH client with the private key from the key pair you selected or created during the instance setup.
3. Connect to your instance using a command like:

```bash
ssh -i /path/to/your-key.pem ec2-user@your-instance-public-dns
