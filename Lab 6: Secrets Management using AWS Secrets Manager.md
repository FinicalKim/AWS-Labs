# Lab 6: Secrets Management using AWS Secrets Manager

## Objective:
Learn how to securely manage application secrets such as database credentials, API keys, and encryption keys using AWS Secrets Manager. You will also learn how to access these secrets programmatically using AWS Lambda and enable automatic rotation.

---

## Step 1: Store a Secret in AWS Secrets Manager

1. **Navigate to the AWS Secrets Manager Console:**
   - Go to the [Secrets Manager Console](https://console.aws.amazon.com/secretsmanager).

2. **Store a New Secret:**
   - Click **Store a new secret**.
   - For **Select secret type**, choose **Credentials for RDS database** (or choose another type if you’re managing different secrets).
   - Enter the database credentials (username and password) or other sensitive information like API keys.
   - Choose an encryption key. You can use the default AWS-managed key or choose a custom KMS key if required.

3. **Configure the Secret:**
   - Provide a secret name (e.g., `MyDatabaseCredentials`).
   - Add optional tags for better tracking or management of your secrets.

4. **Enable Automatic Rotation (Optional):**
   - If you want to enable automatic rotation for your secret, check the **Enable automatic rotation** box.
   - You can set the rotation period (e.g., every 30 days).
   - If you're rotating database credentials, select the Lambda function that will update the database password, or create a new function following the provided template.

5. **Store the Secret:**
   - Click **Next**, review the settings, and click **Store** to save the secret.

---

## Step 2: Access the Secret Programmatically Using AWS Lambda

1. **Create a New Lambda Function:**
   - Navigate to the [Lambda Console](https://console.aws.amazon.com/lambda).
   - Click **Create function**, select **Author from scratch**, and name it `AccessSecretsFunction`.
   - Choose the **Python** or **Node.js** runtime.

2. **Add Permissions to Access Secrets Manager:**
   - Attach an IAM role to the Lambda function that has the `secretsmanager:GetSecretValue` permission.
   - You can create a new role with the following managed policy: `SecretsManagerReadWrite`.

3. **Write the Lambda Function Code:**
   - In the Lambda console, replace the default code with the following (Python example):

     ```python
     import json
     import boto3
     from botocore.exceptions import ClientError

     def get_secret():
         secret_name = "MyDatabaseCredentials"
         region_name = "us-west-2"  # Replace with your region

         # Create a Secrets Manager client
         client = boto3.client('secretsmanager', region_name=region_name)

         try:
             # Retrieve the secret
             get_secret_value_response = client.get_secret_value(SecretId=secret_name)
             secret = get_secret_value_response['SecretString']
             return secret
         except ClientError as e:
             print(f"Error retrieving secret: {e}")
             raise e

     def lambda_handler(event, context):
         secret = get_secret()
         return {
             'statusCode': 200,
             'body': json.dumps(f"Secret retrieved: {secret}")
         }
     ```

4. **Test the Lambda Function:**
   - In the Lambda console, click **Test** and create a new test event with any dummy data.
   - Run the test, and the function should retrieve the stored secret from Secrets Manager and return it as part of the Lambda response.

---

## Step 3: Rotate Secrets Automatically

1. **Enable Rotation for a Secret:**
   - In the **Secrets Manager Console**, select the secret (`MyDatabaseCredentials`) you created earlier.
   - In the **Secret details** section, click **Edit rotation**.

2. **Configure Rotation Settings:**
   - Enable **Automatic rotation** and set the rotation interval (e.g., every 30 days).
   - For database credentials, you will need a Lambda function that handles the update process. AWS provides a default template to create this function.

3. **Test Rotation:**
   - To test rotation, manually trigger it by selecting the secret and choosing **Rotate secret now**.
   - Check that the rotation is successful by reviewing the secret’s **Rotation status**.

4. **Validate Automatic Rotation:**
   - Ensure that the secret is rotated by checking the new value in the Secrets Manager console after the scheduled rotation period.

---

## Validation:

1. **Secret Retrieval:**
   - Test the Lambda function by invoking it to retrieve the stored secret.
   - The function should output the secret's value in the response.

2. **Automatic Rotation:**
   - Confirm that automatic rotation is working by reviewing the secret’s rotation history in the Secrets Manager console.
   - Check that the old secret is updated with a new value after rotation.

---

## Clean Up:

1. **Delete the Lambda Function:**
   - Go to the Lambda console, select the `AccessSecretsFunction`, and delete it.

2. **Delete the Secret:**
   - In the Secrets Manager console, select your secret (`MyDatabaseCredentials`) and choose **Delete secret**.
   - Optionally, set a recovery window if you want the secret to be recoverable for a certain number of days.

3. **Remove IAM Permissions:**
   - If you created a custom IAM role for Lambda, go to the IAM console and delete the role.

---

## Key Learning Points:

- **AWS Secrets Manager:** How to securely store and manage secrets like API keys, database credentials, and encryption keys.
- **Lambda Integration:** How to access secrets programmatically using AWS Lambda and securely retrieve sensitive information.
- **Automatic Rotation:** The importance of automatic secret rotation and how to enable it for continuous security updates.

---
