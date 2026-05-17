
## Step 1 — Create the VPC

1. Go to **AWS Console → VPC → Your VPCs → Create VPC**
2. Fill in the following:
   - **Name tag:** `my-vpc`
   - **IPv4 CIDR block:** `10.0.0.0/16`
   - Leave everything else as default
3. Click **Create VPC**

---

## Step 2 — Create the Internet Gateway

1. Go to **VPC → Internet Gateways → Create internet gateway**
2. **Name tag:** `my-igw`
3. Click **Create internet gateway**
4. On the confirmation screen, click **Actions → Attach to VPC**
5. Select `my-vpc` → Click **Attach internet gateway**

---

## Step 3 — Create the Public Subnet (256 IPs)

1. Go to **VPC → Subnets → Create subnet**
2. Fill in:
   - **VPC ID:** `my-vpc`
   - **Subnet name:** `my-public-subnet`
   - **Availability Zone:** any (e.g. `us-east-1a`)
   - **IPv4 CIDR block:** `10.0.1.0/24` → 256 IPs
3. Click **Create subnet**

**Enable auto-assign public IP:**
- Select `my-public-subnet` → **Actions → Edit subnet settings**
- Enable **Auto-assign public IPv4 address** → **Save**

---

## Step 4 — Create the Private Subnet (256 IPs)

1. Go to **VPC → Subnets → Create subnet**
2. Fill in:
   - **VPC ID:** `my-vpc`
   - **Subnet name:** `my-private-subnet`
   - **Availability Zone:** same or different AZ
   - **IPv4 CIDR block:** `10.0.2.0/24` → 256 IPs
3. Click **Create subnet**

> No public IP assignment needed for the private subnet.

---

## Step 5 — Create the Public Route Table

1. Go to **VPC → Route Tables → Create route table**
2. Fill in:
   - **Name:** `my-public-rt`
   - **VPC:** `my-vpc`
3. Click **Create route table**

**Add internet route:**
- Select `my-public-rt` → **Routes tab → Edit routes → Add route**
- **Destination:** `0.0.0.0/0`
- **Target:** Internet Gateway → `my-igw`
- Click **Save changes**

**Associate with public subnet:**
- **Subnet associations tab → Edit subnet associations**
- Select `my-public-subnet` → **Save associations**

---

## Step 6 — Create the Private Route Table

1. Go to **VPC → Route Tables → Create route table**
2. Fill in:
   - **Name:** `my-private-rt`
   - **VPC:** `my-vpc`
3. Click **Create route table**

> No internet route is added — private subnet uses local routing only.

**Associate with private subnet:**
- **Subnet associations tab → Edit subnet associations**
- Select `my-private-subnet` → **Save associations**

---

## Step 7 — Create a Security Group for EC2

1. Go to **EC2 → Security Groups → Create security group**
2. Fill in:
   - **Name:** `my-ec2-sg`
   - **VPC:** `my-vpc`
3. **Inbound rules → Add rule:**
   - **Type:** SSH
   - **Protocol:** TCP
   - **Port:** 22
   - **Source:** My IP *(or `0.0.0.0/0` for open access — not recommended for production)*
4. Click **Create security group**

---

## Step 8 — Launch the EC2 Instance

1. Go to **EC2 → Instances → Launch instances**
2. Configure:
   - **Name:** `my-linux-instance`
   - **AMI:** Amazon Linux 2023 AMI *(Free tier eligible)*
   - **Instance type:** `t2.micro` *(Free tier)*
   - **Key pair:** Create new or select existing — save the `.pem` file securely
3. **Network settings → Edit:**
   - **VPC:** `my-vpc`
   - **Subnet:** `my-public-subnet`
   - **Auto-assign public IP:** Enable
   - **Security group:** `my-ec2-sg`
4. Leave storage as default (8 GiB gp3)
5. Click **Launch instance**

---

## Step 9 — Connect to the EC2 Instance

