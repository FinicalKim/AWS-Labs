# Lab 1: Identity and Access Management (IAM) Fundamentals

## Objective:
- Create an IAM user with limited permissions.
- Create an IAM role and assign it to an EC2 instance.
- Enable Multi-Factor Authentication (MFA) for the IAM user.

---

## Step 1: Create an IAM User with Limited Permissions

1. **Log in to the AWS Management Console:**
   - Navigate to the [IAM Console](https://console.aws.amazon.com/iam).

2. **Create an IAM User:**
   - In the left-hand menu, click on **Users**, and then click **Add User**.
   - Enter a username, e.g., `limited-user`.
   - Select **Programmatic access** if you want the user to access AWS via the CLI/SDKs, or **AWS Management Console access** if you want them to log in to the console.
   - Set a custom password if you select console access.

3. **Attach Permissions:**
   - Select **Attach policies directly**.
   - Search for and attach the **AmazonEC2ReadOnlyAccess** policy.
   - This will give the user permission to only read EC2 resources (e.g., list EC2 instances).
   - Click **Next**, review the settings, and click **Create User**.

4. **Test Access:**
   - Log out of the AWS console and log back in as the new `limited-user`.
   - Navigate to the **EC2 dashboard** and try to list EC2 instances.
   - Try to perform actions like starting/stopping or launching instances, and you should see an error because of the limited permissions.

---

## Step 2: Create an IAM Role for EC2

1. **Navigate to the

