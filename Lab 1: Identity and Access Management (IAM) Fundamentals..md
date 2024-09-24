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

1. **Navigate to the IAM Roles section:**
   - In the IAM console, click **Roles** on the left-hand menu.

2. **Create a Role:**
   - Click **Create Role**.
   - For the trusted entity, select **AWS Service**, then choose **EC2** from the list.
   - Click **Next: Permissions**.

3. **Attach Permissions to the Role:**
   - In the policy search box, type `AmazonEC2ReadOnlyAccess`.
   - Select the policy and click **Next: Tags**.

4. **Assign a Role Name:**
   - Enter a name for your role, such as `ec2-read-only-role`, and click **Create Role**.

5. **Attach the Role to an EC2 Instance:**
   - Go to the **EC2 dashboard** and select an existing EC2 instance (or create one if you don't have one).
   - Click **Actions** > **Security** > **Modify IAM Role**.
   - Choose the role you just created and assign it to the instance.

6. **Verify Role Functionality:**
   - SSH into the EC2 instance (if you're using a Linux instance).
   - Run the following command to check the instance metadata and confirm it has the IAM role attached:
     ```bash
     curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
     ```
   - You should see the role name returned.

---

## Step 3: Enable MFA for the IAM User

1. **Navigate to the IAM Console:**
   - In the left-hand menu, click **Users** and select the `limited-user` you created.

2. **Enable MFA:**
   - In the **Security credentials** tab, under **Multi-factor Authentication (MFA)**, click **Assign MFA device**.
   - Choose **Virtual MFA device** and follow the steps to scan a QR code using a mobile app like Google Authenticator or Authy.
   - After scanning the QR code, enter two consecutive authentication codes from the app and click **Assign MFA**.

3. **Test MFA:**
   - Log out and try logging back in as the `limited-user`.
   - You should be prompted to enter an MFA code from your authenticator app.

---

## Validation

1. **Permissions Check:**
   - After logging in as `limited-user`, navigate to different AWS services and attempt actions that are outside of the allowed permissions (e.g., creating an EC2 instance). You should see "Access Denied" errors for those actions.

2. **MFA Check:**
   - Make sure that MFA is required every time you log in with `limited-user`.

---

## Clean Up
- If you no longer need the resources, you can delete the IAM user and role:
  - Navigate to **IAM Users**, select `limited-user`, and choose **Delete User**.
  - Navigate to **IAM Roles**, find the role you created, and delete it as well.
  - Ensure any EC2 instance using the IAM role is either terminated or detached from the role.

---

## Key Learning Points:
- **IAM User Permissions:** How to restrict user access with policies.
- **IAM Roles:** How to create and assign roles to services (EC2 in this case).
- **MFA:** Adding an extra layer of security to your AWS account with Multi-Factor Authentication.

---
