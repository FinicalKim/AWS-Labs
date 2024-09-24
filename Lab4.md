# Lab 4: Network Security and VPC Security Groups

## Objective:
Learn how to create secure VPC configurations with Security Groups and Network ACLs (NACLs). Implement least-privilege access to network resources.

---

## Step 1: Create a Custom VPC

1. **Navigate to the VPC Console:**
   - Go to the [VPC Console](https://console.aws.amazon.com/vpc).

2. **Create a New VPC:**
   - In the left-hand menu, click **Your VPCs**, then click **Create VPC**.
   - Enter a name for your VPC (e.g., `lab-vpc`).
   - Choose an IPv4 CIDR block (e.g., `10.0.0.0/16`).
   - Select **Create**.

3. **Create Subnets:**
   - For a public subnet:
     - In the left-hand menu, click **Subnets**, then click **Create subnet**.
     - Choose your VPC, specify a name (e.g., `public-subnet`), and select an availability zone.
     - Assign a CIDR block (e.g., `10.0.1.0/24`).
   - For a private subnet:
     - Repeat the above steps but name it `private-subnet`, and assign a different CIDR block (e.g., `10.0.2.0/24`).

4. **Create an Internet Gateway:**
   - In the left-hand menu, click **Internet Gateways**, then click **Create internet gateway**.
   - Attach the Internet Gateway to your VPC (`lab-vpc`).

5. **Configure Route Tables:**
   - In the left-hand menu, click **Route Tables**.
   - Create a route table for the public subnet and associate the public subnet with this route table.
   - Add a route for all traffic (`0.0.0.0/0`) that directs to the Internet Gateway.
   - Associate the private subnet with the default route table that doesn't route traffic to the internet.

---

## Step 2: Configure Security Groups

1. **Create a Security Group for the Public Subnet:**
   - In the left-hand menu, click **Security Groups**, then click **Create security group**.
   - Name it `public-sg` and select your VPC.
   - Under **Inbound Rules**, add rules to allow:
     - HTTP traffic (Port 80) from `0.0.0.0/0`.
     - SSH traffic (Port 22) from your local IP address only (use `My IP`).
   - Leave the **Outbound Rules** open to all traffic.

2. **Create a Security Group for the Private Subnet:**
   - Create a second security group and name it `private-sg`.
   - Under **Inbound Rules**, allow traffic only from the public subnet’s security group (`public-sg`) for specific services like SSH, HTTP, or application-specific traffic.
   - Leave **Outbound Rules** open to allow all traffic (default setting).

3. **Launch EC2 Instances:**
   - Navigate to the [EC2 Console](https://console.aws.amazon.com/ec2).
   - Launch an EC2 instance in the public subnet and assign it to the `public-sg`.
   - Launch a second EC2 instance in the private subnet and assign it to the `private-sg`.

---

## Step 3: Implement Network ACLs

1. **Create a Network ACL for the Public Subnet:**
   - Navigate to **Network ACLs** in the VPC console.
   - Create a new Network ACL, name it `public-nacl`, and associate it with your public subnet.
   - Add inbound and outbound rules to allow:
     - HTTP (Port 80) and SSH (Port 22) traffic from `0.0.0.0/0`.
     - Keep all other inbound rules as **DENY** by default.
     - Ensure all outbound traffic is allowed.

2. **Create a Network ACL for the Private Subnet:**
   - Create another Network ACL and associate it with the private subnet.
   - Add inbound and outbound rules to allow only traffic from the public subnet’s IP range (e.g., `10.0.1.0/24`).
   - Block all other inbound traffic by leaving the **DENY** rule at the lowest priority.
   - Ensure all outbound traffic is allowed.

3. **Block Traffic from a Specific IP Address:**
   - In your `public-nacl`, create a rule that **DENIES** all traffic from a specific IP address (for testing, you can use a public IP or a specific IP range like `198.51.100.0/24`).

---

## Step 4: Test Your Setup

1. **Connect to the EC2 Instance in the Public Subnet:**
   - Use SSH to connect to the instance using its public IP.
     ```bash
     ssh -i <your-key.pem> ec2-user@<public-ip>
     ```

2. **Connect to the EC2 Instance in the Private Subnet via the Public Instance:**
   - Once logged into the public instance, use SSH to connect to the private instance using its private IP.
     ```bash
     ssh ec2-user@<private-ip>
     ```

3. **Test the Security Group and NACL Rules:**
   - Attempt to access the public EC2 instance via HTTP and SSH from your local machine.
   - Try to access the private instance directly from your local machine (it should be blocked).
   - Test the NACL rule blocking traffic from the specific IP you configured by attempting to access the public instance from that IP.

---

## Validation:

1. **Public Instance Access:**
   - Confirm that the public instance can be accessed over HTTP (port 80) and SSH (port 22), but that the SSH access is restricted to your IP address.

2. **Private Instance Access:**
   - Confirm that the private instance is only accessible from the public instance via SSH, not directly from the internet.

3. **Network ACL Validation:**
   - Test the NACL rules by attempting to connect from an IP address you’ve blocked (using a VPN or a test tool), and verify that the connection is denied.

---

## Clean Up:

1. **Terminate EC2 Instances:**
   - Navigate to the EC2 console, select the instances you created, and terminate them.

2. **Delete the VPC:**
   - Navigate to the VPC console, select your VPC (`lab-vpc`), and delete it. This will also delete associated resources like subnets, route tables, and gateways.

3. **Remove Security Groups and Network ACLs:**
   - Go to the **Security Groups** and **Network ACLs** sections and delete the custom security groups and NACLs you created.

---

## Key Learning Points:

- **Security Groups:** Understanding how to create Security Groups with least-privilege principles and how they function as virtual firewalls at the instance level.
- **Network ACLs:** Learning to configure Network ACLs to provide an additional layer of network control, especially when applying broad deny rules.
- **Public and Private Subnets:** Understanding the differences between public and private subnets in a VPC and how to control access between them.

---
