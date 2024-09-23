# AWS Security Specialty Certification Lab

This lab covers the essential hands-on skills needed for the AWS Certified Security Specialty certification. It includes exercises on IAM, encryption, logging, monitoring, network security, incident response, and more.

## Lab 1: Identity and Access Management (IAM) Fundamentals

### Objective:
Understand how to implement robust IAM policies, roles, and users. Use the principle of least privilege and Multi-Factor Authentication (MFA).

### Steps:
1. **Create an IAM User with Limited Permissions**:
   - Create an IAM user named `limited-user`.
   - Attach a policy that only allows this user to list EC2 instances (`ec2:DescribeInstances`).

2. **Create an IAM Role for EC2**:
   - Create an IAM role that provides EC2 read-only access (`AmazonEC2ReadOnlyAccess` policy).
   - Attach the role to an existing EC2 instance.

3. **Enable MFA**:
   - For the `limited-user`, enable MFA using a virtual MFA device.
   - Test access with and without MFA enabled.

### Validation:
- Log in as `limited-user` and attempt to perform actions outside the scope of allowed permissions.
- Verify MFA works by logging in and being prompted for the second factor.

---

## Lab 2: CloudTrail and Logging

### Objective:
Configure AWS CloudTrail for auditing and logging purposes. Understand how to use logs for monitoring suspicious activity.

### Steps:
1. **Enable CloudTrail**:
   - Create a new CloudTrail named `security-lab-trail`.
   - Enable logging for all regions.
   - Store logs in an S3 bucket.

2. **Log S3 and IAM Activity**:
   - Enable logging for S3 data events and IAM management events.

3. **Create a CloudWatch Alarm**:
   - Set up a CloudWatch alarm that triggers if there are more than 5 failed login attempts from IAM users within 5 minutes.
   - Create an SNS topic for email notifications.

### Validation:
- Perform an action like deleting an S3 bucket or launching an EC2 instance.
- Review CloudTrail logs to find the recorded event.
- Simulate failed login attempts and check if the CloudWatch alarm triggers.

---

## Lab 3: Encryption and Data Protection

### Objective:
Learn to secure sensitive data at rest and in transit using AWS Key Management Service (KMS) and SSL/TLS encryption.

### Steps:
1. **Create and Use KMS Key**:
   - Create a KMS key in your account.
   - Create an S3 bucket and enable server-side encryption (SSE-KMS) using the KMS key.

2. **Enable Encryption in Transit**:
   - Launch an RDS instance.
   - Enable SSL connections for your RDS instance.

3. **Create and Rotate Access Keys**:
   - Create access keys for an IAM user.
   - Rotate the access keys by creating new ones and disabling the old ones.

### Validation:
- Upload a file to the S3 bucket and ensure that it is encrypted using the KMS key.
- Connect to the RDS instance using SSL and verify the encrypted connection.

---

## Lab 4: Network Security and VPC Security Groups

### Objective:
Learn how to create secure VPC configurations with Security Groups and Network ACLs. Implement least-privilege access to network resources.

### Steps:
1. **Create a Custom VPC**:
   - Create a VPC with public and private subnets.
   - Launch EC2 instances in both subnets.

2. **Configure Security Groups**:
   - Create a security group for public EC2 instances that only allows HTTP and SSH traffic.
   - Create a security group for private EC2 instances that allows traffic only from the public subnet.

3. **Implement Network ACLs**:
   - Create a Network ACL that blocks all traffic from a specific IP range.
   - Attach the ACL to the public subnet.

### Validation:
- Attempt to access the public and private instances from your local machine and verify that the Security Groups and ACLs are working as expected.
- Test the network ACL by attempting to connect from a blocked IP range.

---

## Lab 5: Incident Response with AWS Config and GuardDuty

### Objective:
Learn how to detect and respond to security incidents using AWS Config and Amazon GuardDuty.

### Steps:
1. **Enable AWS Config**:
   - Enable AWS Config to track changes to your AWS resources.
   - Configure a rule to check for unrestricted S3 bucket access.

2. **Enable GuardDuty**:
   - Enable Amazon GuardDuty in your account.
   - Simulate suspicious activity (such as a port scan) to generate a GuardDuty finding.

3. **Respond to a Security Incident**:
   - Configure an SNS notification for high-severity GuardDuty findings.
   - Create an automatic remediation workflow using AWS Lambda that triggers upon certain findings (e.g., unauthorized access to EC2 instances).

### Validation:
- Simulate a security incident by opening an S3 bucket to the public, then verify that AWS Config flags this violation.
- Review GuardDuty findings and respond accordingly with the Lambda function.

---

## Lab 6: Secrets Management using AWS Secrets Manager

### Objective:
Learn how to securely manage application secrets such as database credentials, API keys, and encryption keys using AWS Secrets Manager.

### Steps:
1. **Store a Secret**:
   - Create a new secret in AWS Secrets Manager to store RDS database credentials.

2. **Access the Secret Programmatically**:
   - Create a Lambda function that retrieves the secret from AWS Secrets Manager and uses it to connect to an RDS instance.

3. **Rotate Secrets Automatically**:
   - Set up automatic rotation of the secret.

### Validation:
- Verify that the secret is stored securely and can be accessed via the Lambda function.
- Check that automatic rotation works without service interruption.

---

## Lab 7: AWS Shield and WAF

### Objective:
Implement DDoS protection and web application security using AWS Shield and AWS Web Application Firewall (WAF).

### Steps:
1. **Enable AWS Shield**:
   - Enable AWS Shield Standard for your account.
   - Simulate a DDoS attack scenario (using test tools or traffic simulation).

2. **Configure AWS WAF**:
   - Create a Web ACL in AWS WAF that blocks common threats such as SQL injection and cross-site scripting (XSS).
   - Attach the Web ACL to an existing CloudFront distribution.

### Validation:
- Test your WAF rules by running sample attacks (SQL injection, XSS) and observe how the WAF blocks them.
- Review Shield metrics and logs for DDoS mitigation details.

---

## Final Tips:
- Ensure that you clean up resources after each lab (e.g., terminate EC2 instances, delete CloudTrail logs, S3 buckets, and KMS keys) to avoid unnecessary charges.
- Pay special attention to AWS security best practices, such as the Shared Responsibility Model, encryption, and network segmentation.
