# AWS Environment Setup with EC2 Instances

This guide walks you through the process of setting up your AWS environment and launching EC2 instances.

## Step 1: Log in to the AWS Management Console

1. Navigate to the [AWS Management Console](https://aws.amazon.com/console/).
2. Log in using your AWS account credentials.
![image](https://github.com/KBola98/aws-cloud-monitoring/assets/52285719/028a0510-4f9e-4311-a262-42d61cffb8de)

## Step 2: Access the EC2 Dashboard

1. In the AWS Management Console, locate and click on the **Services** menu at the top.
2. Find and select **EC2** under the **Compute** category to open the EC2 Dashboard.

## Step 3: Launch a New EC2 Instance

1. Click on the **Launch Instances** button to start the instance setup process.
2. Follow the on-screen instructions to select an Amazon Machine Image (AMI), instance type, and configure your instance settings. You'll also configure a key pair for SSH access to the instance.
3. Review your instance configurations. Ensure that everything is set according to your requirements.
4. Launch your instance.

## Connecting to Your Instance via SSH

Once the instance is running, you can connect to it using SSH:

1. The AWS Console provides the public DNS or IP address and the connection instructions.
2. Use an SSH client with the private key from the key pair you selected or created during the instance setup.
3. Connect to your instance using a command like:

```bash
ssh -i /path/to/your-key.pem ec2-user@your-instance-public-dns
