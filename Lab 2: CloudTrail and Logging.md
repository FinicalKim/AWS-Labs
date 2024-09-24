# Lab 2: CloudTrail and Logging

## Objective:
Configure AWS CloudTrail for auditing and logging purposes. Understand how to use logs for monitoring suspicious activity and creating alarms for specific events.

---

## Step 1: Enable CloudTrail

1. **Log in to the AWS Management Console:**
   - Navigate to the [CloudTrail Console](https://console.aws.amazon.com/cloudtrail).

2. **Create a New CloudTrail:**
   - In the CloudTrail dashboard, click **Create Trail**.
   - Name the trail `security-lab-trail`.
   - Select **Apply trail to all regions** to ensure that activity is logged across all regions.
   - For **Management Events**, enable both **Read/Write events** (default).

3. **Store Logs in an S3 Bucket:**
   - Under **Storage location**, select **Create a new S3 bucket** and give the bucket a unique name like `cloudtrail-logs-security-lab`.
   - Ensure **Enable log file validation** is selected for integrity checks.
   
4. **Set Up Encryption (Optional):**
   - If you require extra security, you can use **Server-Side Encryption with AWS KMS** by selecting an existing KMS key or creating a new one.

5. **Create the Trail:**
   - Review the settings, and click **Create Trail**.

6. **Verify Trail Functionality:**
   - After the trail is created, AWS will start logging events to the S3 bucket you specified.
   - You can review the logs by navigating to the S3 bucket and exploring the log files.

---

## Step 2: Log S3 and IAM Activity

1. **Enable Logging for S3 Data Events:**
   - In the CloudTrail console, click on your trail (`security-lab-trail`).
   - Scroll down to the **Data events** section, click **Add S3 bucket**.
   - Select the bucket you want to log data events for (e.g., `cloudtrail-logs-security-lab`), and click **Add**.

2. **Enable IAM Management Event Logging:**
   - By default, IAM management events are logged as part of the **Management events** that you enabled earlier.
   - To verify, go to the **Management Events** section and confirm **Read/Write** logging is enabled for IAM.

---

## Step 3: Create a CloudWatch Alarm for Security Events

1. **Navigate to CloudWatch Console:**
   - Go to the [CloudWatch Console](https://console.aws.amazon.com/cloudwatch).

2. **Create a Metric Filter for Failed Login Attempts:**
   - In the left-hand menu, under **Logs**, click **Log groups**.
   - Select the log group associated with your CloudTrail trail (e.g., `/aws/cloudtrail/<region>`).
   - Click **Create Metric Filter**, then click **Assign Metric**.
   - Define a filter pattern to capture failed login attempts. Use the following pattern:
     ```json
     { $.eventName = "ConsoleLogin", $.errorMessage = "Failed authentication" }
     ```
   - Name the metric filter (e.g., `FailedConsoleLogins`) and create a new metric namespace like `CloudTrailMetrics`.

3. **Create an Alarm Based on the Metric:**
   - Go to **Alarms** and click **Create Alarm**.
   - Select the **FailedConsoleLogins** metric from the `CloudTrailMetrics` namespace.
   - Set the threshold to trigger the alarm when there are more than **5 failed login attempts** in 5 minutes.
   - Configure the notification action by creating an SNS topic (e.g., `FailedLoginsTopic`) and subscribe your email to this topic.

4. **Test the Alarm:**
   - To test, attempt to log in with incorrect credentials several times. After five failed attempts, the CloudWatch alarm should trigger, and you should receive an email notification.

---

## Validation:

1. **Verify CloudTrail Logs:**
   - Perform some basic actions in the AWS Console, such as launching or terminating an EC2 instance.
   - Navigate to your S3 bucket and review the CloudTrail log files to see if the actions are recorded.

2. **Check S3 Data Logging:**
   - Upload or delete a file in the S3 bucket you added for data events.
   - Review the CloudTrail logs and verify that the S3 data events are captured.

3. **Test CloudWatch Alarm:**
   - Simulate failed login attempts (e.g., try logging in with incorrect passwords).
   - After the specified threshold is crossed (5 failed attempts), check if the CloudWatch alarm triggers and you receive a notification email.

---

## Clean Up:

- **Disable/Delete CloudTrail Trail:**
   - If you no longer need the trail, navigate to the CloudTrail console, select your trail, and click **Delete**.
   
- **Remove S3 Bucket and Logs:**
   - If you created a new S3 bucket, delete it by navigating to the **S3 console**, selecting the bucket, and choosing **Delete**.

- **Delete the CloudWatch Alarm and Metric Filter:**
   - Go to the **CloudWatch Console**, find the alarm you created, and delete it.
   - Also, remove the metric filter from the CloudWatch Logs if it’s no longer needed.

---

## Key Learning Points:

- **CloudTrail:** How to set up CloudTrail to log AWS API events and ensure data is available for auditing.
- **CloudWatch Alarms:** How to monitor specific events, such as failed login attempts, using CloudWatch Logs and alarms.
- **S3 Data Event Logging:** How to track detailed S3 object-level operations for better insight into security-related activities.

---
