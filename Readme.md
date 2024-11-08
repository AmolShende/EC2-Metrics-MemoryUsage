Monitoring memory usage for EC2 instances in CloudWatch is not enabled by default, as AWS CloudWatch only provides basic metrics (like CPU utilization, disk usage, and network activity). To monitor memory usage, you need to install the **CloudWatch agent** on your EC2 instances to collect custom metrics. Here’s a step-by-step guide:

### Step 1: Install the CloudWatch Agent
1. **SSH into your EC2 instance**:
   - Access your EC2 instance using SSH.
   
2. **Install the CloudWatch Agent**:
   - For **Amazon Linux** or **Amazon Linux 2**, run:
     ```bash
     sudo yum install amazon-cloudwatch-agent -y
     ```
   - For **Ubuntu**, run:
     ```bash
     sudo apt-get update
     sudo apt-get install amazon-cloudwatch-agent -y
     ```

3. **Create an IAM Role for the CloudWatch Agent**:
   - In the IAM console, create a role with the **AmazonCloudWatchAgentServerPolicy** policy attached.
   - Attach this role to your EC2 instance so it has permissions to send metrics to CloudWatch.

### Step 2: Configure the CloudWatch Agent
1. **Create the CloudWatch Agent Configuration File**:
   - Create or edit the agent configuration file at `/opt/aws/amazon-cloudwatch-agent/bin/config.json`.
   - Example configuration for memory monitoring:
     ```json
     {
       "metrics": {
         "append_dimensions": {
           "InstanceId": "${aws:InstanceId}"
         },
         "metrics_collected": {
           "mem": {
             "measurement": [
               "mem_used_percent",
               "mem_available",
               "mem_total"
             ],
             "metrics_collection_interval": 60
           }
         }
       }
     }
     ```

2. **Use the CloudWatch Agent Configuration Wizard (Optional)**:
   - Run the configuration wizard to generate the configuration file interactively:
     ```bash
     sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
     ```
   - The wizard will guide you through setting up memory and other metrics.

### Step 3: Start the CloudWatch Agent
- Start the CloudWatch Agent to begin collecting memory metrics and sending them to CloudWatch:
  ```bash
  sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a start
  ```

### Step 4: Verify and View Memory Metrics in CloudWatch
1. Go to the **CloudWatch Console**.
2. Navigate to **Metrics** > **All Metrics**.
3. Select **CWAgent** (the namespace used by the CloudWatch agent).
4. Choose **InstanceId** to view memory metrics for your instance, such as:
   - **mem_used_percent**: Percentage of memory used.
   - **mem_available**: Available memory in bytes.
   - **mem_total**: Total memory in bytes.

### Step 5: Create Alarms (Optional)
1. You can create a CloudWatch alarm to notify you if memory utilization exceeds a certain threshold.
2. In the CloudWatch console, go to **Alarms** > **Create alarm**.
3. Select a memory metric (like **mem_used_percent**), set a threshold, and configure a notification action.

With this setup, you can monitor memory usage and get notified if usage is high, allowing you to troubleshoot and optimize your EC2 resources.
