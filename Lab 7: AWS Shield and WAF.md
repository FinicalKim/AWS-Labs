# Lab 7: AWS Shield and WAF

## Objective:
Implement Distributed Denial of Service (DDoS) protection using AWS Shield and protect a web application with AWS Web Application Firewall (WAF). You will also configure rules to block malicious traffic and respond to threats.

---

## Step 1: Enable AWS Shield

1. **Navigate to the AWS Shield Console:**
   - Go to the [AWS Shield Console](https://console.aws.amazon.com/wafv2/shield).

2. **Shield Standard (Default Protection):**
   - AWS Shield Standard is automatically enabled for all AWS accounts. It provides protection against common network and transport layer DDoS attacks.
   - You don’t need to take any additional steps to enable AWS Shield Standard.

3. **Test AWS Shield Standard Protection:**
   - While you cannot simulate real DDoS attacks without risking service disruptions, you can view the default protection that AWS Shield provides by reviewing the **DDoS reports** in the Shield console.
   - These reports provide details on any attacks or mitigated threats automatically handled by Shield.

---

## Step 2: Configure AWS WAF with CloudFront

1. **Navigate to the AWS WAF Console:**
   - Go to the [WAF Console](https://console.aws.amazon.com/wafv2).

2. **Create a Web ACL:**
   - In the WAF console, click **Create Web ACL**.
   - Name the Web ACL (e.g., `MyWAFWebACL`) and choose the appropriate AWS resource type. For this lab, choose **CloudFront distributions** (CloudFront integrates with WAF to protect your web application).
   - Select an existing CloudFront distribution or create a new one if needed.

3. **Add WAF Rules:**
   - In the **Add rules** section, click **Add managed rule groups**.
   - Choose AWS Managed Rules (e.g., **AWSManagedRulesCommonRuleSet**) which includes predefined protections for SQL injection, cross-site scripting (XSS), and other threats.
   - You can also create custom rules:
     - **SQL Injection Protection:** Click **Add my own rules and rule groups**, and select **Add Rule** > **Create your own rule**. Set the rule to inspect incoming requests for SQL injection patterns.
     - **IP Block Rule:** Create a rule to block traffic from a specific IP range. In the rule configuration, select **IP Set** and define an IP range to block (e.g., `203.0.113.0/24`).

4. **Set Default Action:**
   - Configure the default action for your Web ACL (e.g., **Allow** or **Block** all traffic that does not match any rules).
   - For this lab, set the default action to **Allow** and let specific rules handle blocking malicious traffic.

5. **Associate Web ACL with CloudFront:**
   - Associate the Web ACL with your CloudFront distribution. This ensures that incoming requests are inspected and filtered by WAF before they reach your web application.

---

## Step 3: Test WAF Rules

1. **Simulate a Web Request to Test WAF Rules:**
   - If you have a website or application served via CloudFront, try accessing it to ensure it works as expected.
   - Test specific WAF rules by simulating attacks (e.g., trying to insert SQL queries into URL parameters).
   - If you’ve configured IP blocking, try accessing the site from an IP that is in the blocked range.

2. **Monitor WAF Logs:**
   - Navigate to the **Logging and metrics** section of the WAF console.
   - Enable logging for your Web ACL by creating a CloudWatch log group or sending logs to S3.
   - Review the logs to confirm that WAF is blocking malicious requests or requests from blocked IPs.

---

## Step 4: Enable AWS Shield Advanced (Optional)

1. **Shield Advanced Protection (Optional):**
   - If you require additional DDoS protection beyond Shield Standard, you can subscribe to **AWS Shield Advanced**.
   - Shield Advanced offers enhanced protections, including financial protection, advanced attack mitigation, and 24/7 access to the AWS DDoS Response Team (DRT).
   
2. **Enable Shield Advanced:**
   - In the Shield console, click **Get started with Shield Advanced**.
   - Select resources to protect (e.g., EC2, Elastic Load Balancing, CloudFront, or Route 53 resources).
   - You’ll be prompted to configure enhanced protections, such as anomaly detection and health checks.
   
3. **Simulate a DDoS Attack with Shield Advanced (Optional):**
   - AWS provides test tools that allow you to simulate DDoS attacks in non-production environments to test Shield Advanced’s effectiveness.
   - Contact AWS Support if you need assistance with simulating attacks in a controlled environment.

---

## Step 5: Create CloudWatch Alarms for Threats

1. **Navigate to the CloudWatch Console:**
   - Go to the [CloudWatch Console](https://console.aws.amazon.com/cloudwatch).

2. **Create an Alarm for WAF and Shield Events:**
   - In the left-hand menu, click **Alarms**, then **Create Alarm**.
   - Select the **WAF ACL Rule Metric** or **Shield DDoS Events** metric from the list of available metrics.
   - Set the threshold for the number of blocked requests or detected DDoS events.
   - Configure the alarm to send notifications via Amazon SNS by creating an SNS topic and subscribing your email to it.

3. **Test the CloudWatch Alarm:**
   - Trigger the alarm by simulating malicious requests (e.g., SQL injection or a large number of requests from a blocked IP).
   - Verify that you receive a notification via SNS when the threshold is crossed.

---

## Validation:

1. **Test WAF Rules:**
   - Ensure that the rules in your Web ACL are working by simulating web traffic that matches the rule conditions (e.g., SQL injection attacks or IP-based blocking).
   - Verify that legitimate traffic is not blocked.

2. **Monitor Shield Reports:**
   - Go to the AWS Shield console and review the **DDoS protection reports** to see if any DDoS attacks were detected or mitigated.
   
3. **Check CloudWatch Logs and Alarms:**
   - Review the logs generated by WAF in CloudWatch to verify that the correct rules are being triggered.
   - Ensure CloudWatch alarms are functioning correctly and notifications are sent when thresholds are crossed.

---

## Clean Up:

1. **Delete WAF Web ACL:**
   - Navigate to the WAF console and delete the Web ACL (`MyWAFWebACL`) you created, including any associated rules.

2. **Disable CloudFront Distribution:**
   - If you no longer need the CloudFront distribution used in this lab, navigate to the CloudFront console and disable or delete the distribution.

3. **Disable AWS Shield Advanced (Optional):**
   - If you enabled Shield Advanced, navigate to the Shield console and disable the service to avoid ongoing charges.

4. **Remove CloudWatch Alarms:**
   - Go to the CloudWatch console, find the alarms you created for WAF and Shield, and delete them.

---

## Key Learning Points:

- **AWS Shield:** Understanding the default protections provided by AWS Shield Standard and how to enable AWS Shield Advanced for additional DDoS protection.
- **AWS WAF:** How to create Web ACLs, configure managed and custom rules, and block malicious traffic to your web applications.
- **CloudWatch Monitoring:** Using CloudWatch to create alarms and monitor security-related events like blocked WAF requests or detected DDoS attacks.

---
