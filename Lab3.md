# Lab 3: Encryption and Data Protection

## Objective:
Learn to secure sensitive data at rest and in transit using AWS Key Management Service (KMS) and SSL/TLS encryption.

---

## Step 1: Create and Use a KMS Key

1. **Navigate to the KMS Console:**
   - Go to the [KMS Console](https://console.aws.amazon.com/kms).

2. **Create a New KMS Key:**
   - In the left-hand menu, click **Customer Managed Keys**, then click **Create Key**.
   - Select **Symmetric** key type and click **Next**.
   - Provide an alias for the key, such as `my-security-lab-key`.
   - Add any required tags for tracking purposes.

3. **Configure Key Administrators and Users:**
   - Add yourself as a key administrator.
   - Define IAM users or roles that can use the key for encryption/decryption operations (you can select specific users or use an EC2 instance role if needed).
   - Click **Finish** to create the KMS key.

4. **Create an S3 Bucket and Enable Server-Side Encryption (SSE-KMS):**
   - Navigate to the [S3 Console](https://console.aws.amazon.com/s3).
   - Create a new S3 bucket and give it a unique name (e.g., `kms-encrypted-bucket`).
   - Go to the **Properties** tab for the bucket, find the **Default encryption** section, and click **Edit**.
   - Select **AWS Key Management Service key (SSE-KMS)** and choose the KMS key you just created.
   - Save the changes.

5. **Test Encryption:**
   - Upload a file to the S3 bucket.
   - Navigate to the file's **Properties** in S3 and confirm that server-side encryption is enabled with the KMS key.

---

## Step 2: Enable Encryption in Transit with RDS

1. **Launch an RDS Instance:**
   - Navigate to the [RDS Console](https://console.aws.amazon.com/rds).
   - Click **Create database** and choose a database engine (e.g., MySQL, PostgreSQL).
   - Use the default settings for the DB instance, but ensure **Enable Encryption** is selected (this encrypts the storage at rest).

2. **Enable SSL Connections:**
   - After the RDS instance is created, navigate to the **Connectivity & security** tab for the instance.
   - Download the **RDS SSL Certificate** from the [AWS RDS SSL documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.SSL.html).
   - On your local machine, configure your database client to connect using SSL by referencing the SSL certificate.

3. **Test SSL Connection:**
   - Use a database client (e.g., MySQL Workbench, pgAdmin) to connect to the RDS instance.
   - Make sure that SSL is enabled by checking the client configuration or viewing the SSL connection details after logging in.

---

## Step 3: Create and Rotate Access Keys

1. **Create Access Keys for an IAM User:**
   - Go to the [IAM Console](https://console.aws.amazon.com/iam).
   - Click on **Users**, and select the user for whom you want to create access keys.
   - In the **Security credentials** tab, click **Create access key**.
   - Note down the access key ID and secret access key (these will only be shown once).

2. **Rotate the Access Keys:**
   - To rotate the access keys, first create a new access key following the steps above.
   - Disable the old access key but keep it active for a grace period during testing.
   - Once you confirm the new access key is functioning correctly, delete the old one.

---

## Validation:

1. **Verify S3 Encryption:**
   - Upload a file to the S3 bucket with KMS encryption.
   - Navigate to the **Properties** tab of the uploaded file and confirm the KMS encryption is applied by checking the **Encryption** field.

2. **Test RDS SSL Connection:**
   - Check your database client logs or connection details to verify that the connection to the RDS instance is encrypted via SSL.
   - Perform queries and confirm normal functionality with encryption in transit.

3. **Access Key Rotation:**
   - After rotating the access keys, attempt to use the old key (which should fail) and the new key (which should succeed) in operations like listing S3 buckets via the AWS CLI:
     ```bash
     aws s3 ls --profile <profile-name>
     ```

---

## Clean Up:

1. **Delete S3 Bucket and Files:**
   - Navigate to the S3 console, select the bucket you created (`kms-encrypted-bucket`), and delete it, including all uploaded files.

2. **Terminate RDS Instance:**
   - In the RDS console, select your database instance and click **Delete**.
   - Make sure to create a final snapshot if necessary, and delete any associated backups.

3. **Delete the KMS Key:**
   - Go to the KMS console, select your KMS key (`my-security-lab-key`), and choose **Schedule key deletion**.
   - Choose a deletion window (minimum 7 days) to ensure there is no immediate removal of the key.

---

## Key Learning Points:

- **KMS Encryption:** How to create and manage KMS keys for securing data at rest, and how to use them with S3.
- **SSL/TLS Encryption:** How to enable encryption in transit for RDS and ensure secure database connections.
- **Access Key Rotation:** The importance of access key rotation and secure handling of credentials.

---
