# Lab 5: Incident Response with AWS Config and GuardDuty

## Objective:
Learn how to detect and respond to security incidents using AWS Config and Amazon GuardDuty. Set up rules to monitor and manage security violations, and configure GuardDuty to identify potential threats.

---

## Step 1: Enable AWS Config

1. **Navigate to the AWS Config Console:**
   - Go to the [AWS Config Console](https://console.aws.amazon.com/config).

2. **Configure AWS Config:**
   - Click **Get started** if AWS Config isn’t already enabled.
   - Select **All resources** for resource types to be recorded.
   - For the **Amazon S3 bucket** to store configuration history, you can either choose an existing bucket or create a new one (e.g., `config-logs-bucket`).
   - Enable **AWS Config rules** to evaluate resource compliance.

3. **Configure a Rule for Unrestricted S3 Bucket Access:**
   - In the AWS Config console, click **Rules**, then click **Add rule**.
   - Search for the rule named **s3-bucket-public-read-prohibited** and select it.
   - This rule will trigger if any S3 bucket is publicly accessible.
   - Leave the default rule parameters and click **Add rule**.

4. **Monitor AWS Config for Compliance Violations:**
   - After configuring the rule, AWS Config will automatically scan your S3 buckets.
   - If an S3 bucket is found to be publicly accessible, the rule will mark the resource as non-compliant.
   - Check the **Resources** tab for a list of any non-compliant resources.

---

## Step 2: Enable Amazon GuardDuty

1. **Navigate to the GuardDuty Console:**
   - Go to the [GuardDuty Console](https://console.aws.amazon.com/guardduty).

2. **Enable GuardDuty:**
   - In the GuardDuty dashboard, click **Enable GuardDuty**.
   - GuardDuty will start monitoring your account for suspicious activity automatically.
   
3. **Simulate Suspicious Activity:**
   - You can simulate a security incident by using AWS’s provided GuardDuty sample findings:
     - In the GuardDuty console, click **Settings** in the left-hand menu.
     - Scroll down to **Sample Findings**, and click **Generate sample findings**.
   - These findings will simulate various types of suspicious activity like port scanning or unauthorized access attempts.

4. **Review GuardDuty Findings:**
   - Once GuardDuty has been running for a while, go to the **Findings** tab.
   - Review the findings for any high-severity issues. GuardDuty will categorize threats such as reconnaissance, compromised instances, or unusual API activity.
   - Check the details of the simulated findings and real findings to understand their impact.

---

## Step 3: Automate Incident Response Using SNS and Lambda

1. **Create an SNS Topic for Alerts:**
   - Go to the [SNS Console](https://console.aws.amazon.com/sns).
   - Click **Create Topic**, and choose **Standard** for the type.
   - Name the topic `GuardDutyAlerts`.
   - Create a subscription for the topic by adding your email address or phone number, so you receive notifications when GuardDuty detects a finding.

2. **Configure GuardDuty to Use SNS:**
   - In the GuardDuty console, go to **Settings** > **Configure SNS Notifications**.
   - Choose the SNS topic (`GuardDutyAlerts`) you created, and enable notifications for findings of all severity levels (low, medium, and high).

3. **Create a Lambda Function for Automated Remediation:**
   - Navigate to the [Lambda Console](https://console.aws.amazon.com/lambda) and click **Create function**.
   - Choose **Author from scratch** and name the function `AutoRemediateEC2`.
   - Choose the **Python** or **Node.js** runtime and create the function.
   - In the **Function Code** section, paste the following code to automatically stop EC2 instances flagged by GuardDuty as compromised:

     ```python
     import json
     import boto3

     ec2 = boto3.client('ec2')

     def lambda_handler(event, context):
         # Parse the GuardDuty finding details
         instance_id = event['detail']['resource']['instanceDetails']['instanceId']
         # Stop the EC2 instance
         response = ec2.stop_instances(InstanceIds=[instance_id])
         print(f'Stopped instance: {instance_id}')
     ```

4. **Configure the Lambda Trigger:**
   - In the Lambda console, click **Add trigger**.
   - Select **CloudWatch Events** (also called EventBridge).
   - Create a new rule that triggers based on GuardDuty findings where the `severity` is greater than a certain value (e.g., severity `> 7` for high severity).
   - Set the rule to invoke the `AutoRemediateEC2` Lambda function.

5. **Test the Automation:**
   - Generate a sample GuardDuty finding with a high severity score (simulating a compromised EC2 instance).
   - If the finding relates to an EC2 instance, the Lambda function should automatically stop the instance, and you should receive an SNS alert.

---

## Validation:

1. **AWS Config Validation:**
   - Make one of your S3 buckets publicly accessible, then check AWS Config to verify that the rule triggers and marks the bucket as non-compliant.
   - Make the bucket private again and ensure AWS Config updates its compliance status.

2. **GuardDuty Findings:**
   - Review the GuardDuty findings for any real threats or sample threats.
   - Confirm that SNS notifications are received for high-severity findings.

3. **Lambda Response Automation:**
   - Ensure that the Lambda function stops any EC2 instance flagged as compromised by GuardDuty.
   - Check the CloudWatch logs for the Lambda function to verify it successfully executed.

---

## Clean Up:

1. **Disable GuardDuty:**
   - Go to the GuardDuty console and click **Disable GuardDuty** to stop monitoring your account and generating findings.

2. **Delete SNS Topic:**
   - Go to the SNS console, select the `GuardDutyAlerts` topic, and delete it.

3. **Remove AWS Config Rules:**
   - Navigate to the AWS Config console and delete the S3 public access rule (`s3-bucket-public-read-prohibited`).

4. **Delete Lambda Function:**
   - Go to the Lambda console, find the `AutoRemediateEC2` function, and delete it.

---

## Key Learning Points:

- **AWS Config:** How to track and enforce compliance of AWS resources using rules and automatic evaluations.
- **Amazon GuardDuty:** How to enable GuardDuty for threat detection and monitor account activity for security risks.
- **Automated Remediation:** Using SNS and Lambda to automatically respond to security threats like compromised EC2 instances.

---
