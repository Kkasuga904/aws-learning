## S3 Reflection - April 26, 2025
✅ Created buckets via console and CLI
✅ Set up lifecycle rule: 7日後にオブジェクトを削除するルールを設定した。
⬜ Hosted static website using S3 + CloudFront
💡 Learned how IAM roles and bucket policies work: バケットポリシーでIPアドレスによるアクセス制御や、サーバーアクセスログの書き込み許可などを設定した。IAMロールはまだ理解が浅いので、EC2の学習で深めたい。
Next: Launch EC2 instance & SSH into it

Next: Launch EC2 instance & SSH into it
確認用の変更

# Small-Scale Infrastructure Construction (Manual)

## Architecture Diagram

[architecture.drawio](architecture.drawio) (Created with draw.io)

(Brief description or overview of the architecture diagram here)

## Manual Construction Steps

### 1. Create the VPC

* Log in to the AWS Management Console.
* Navigate to the VPC Dashboard (<https://console.aws.amazon.com/vpc/>).
* In the left navigation pane, select "Your VPCs" and click "Create VPC".
* For "Name tag (optional)", enter a name for the VPC (e.g., `my-vpc`).
* For "IPv4 CIDR block", enter `10.0.0.0/16`.
* Leave other settings as default and click "Create VPC".

### 2. Create the Public Subnet

* In the VPC Dashboard's left navigation pane, select "Subnets" and click "Create subnet".
* For "VPC ID", select the created VPC (`my-vpc`).
* For "Name tag (optional)", enter a name for the subnet (e.g., `public-subnet-1`).
* For "Availability Zone", select an Availability Zone (e.g., `ap-northeast-1a`).
* For "IPv4 CIDR block", enter `10.0.1.0/24`.
* Leave other settings as default and click "Create subnet".

### 3. Create the Internet Gateway

* In the VPC Dashboard's left navigation pane, select "Internet Gateways" and click "Create internet gateway".
* For "Name tag (optional)", enter a name for the Internet Gateway (e.g., `my-igw`) and click "Create internet gateway".

### 4. Attach the Internet Gateway to the VPC

* Select the created Internet Gateway, click "Actions", and then "Attach to VPC".
* For "VPC ID", select the created VPC (`my-vpc`) and click "Attach internet gateway".

### 5. Create the Route Table

* In the VPC Dashboard's left navigation pane, select "Route tables" and click "Create route table".
* For "VPC ID", select the created VPC (`my-vpc`).
* For "Name tag (optional)", enter a name for the Route Table (e.g., `public-rt`) and click "Create route table".

### 6. Add a Route to the Internet Gateway in the Public Subnet's Route Table

* Select the created Route Table (`public-rt`), go to the "Routes" tab, and click "Edit routes".
* Click "Add route", enter `0.0.0.0/0` for "Destination", and select the created Internet Gateway (`my-igw`) for "Target". Click "Save changes".

### 7. Associate the Public Subnet with the Route Table

* Select the created Route Table (`public-rt`), go to the "Subnet associations" tab, and click "Edit subnet associations".
* Select the created Public Subnet (`public-subnet-1`) and click "Save associations".

### 8. Create the EC2 Instance

* Navigate to the EC2 Dashboard (<https://console.aws.amazon.com/ec2/>) and click "Launch instance".
* For "Application and OS Images (Amazon Machine Image)", choose "Amazon Linux 2 AMI (HVM), SSD Volume Type".
* For "Instance type", choose "t2.micro".
* Click "Next: Configure Instance Details".
* For "Network", select the created VPC (`my-vpc`).
* For "Subnet", select the created Public Subnet (`public-subnet-1`).
* For "Auto-assign Public IP", choose "Enable".
* Leave other settings as default and click "Next: Add Storage".
* Leave storage settings as default and click "Next: Add Tags".
* Add tags as needed and click "Next: Configure Security Group".
* Create a new security group or select an existing one. Add an inbound rule for SSH (port 22) with the source set to "My IP" or a suitable range.
* Click "Review and Launch", review the details, and click "Launch".
* Create a new key pair or select an existing one, download the private key file to a secure location, and click "Launch Instances".

## Description of Resources

* **VPC (my-vpc, 10.0.0.0/16):** A private network created in AWS. Other resources are built within this VPC. The CIDR block `10.0.0.0/16` defines the IP address range for this VPC.


## 構成図

![AWS Infrastructure Diagram](architecture.drawio.png)
* **Public Subnet (public-subnet-1, 10.0.1.0/24, ap-northeast-1a):** A subnet within the VPC that has a route to the internet. The CIDR block `10.0.1.0/24` defines the IP address range for this subnet. `ap-northeast-1a` is the Availability Zone where this subnet resides.
* **Internet Gateway (my-igw):** A gateway that enables communication between the VPC and the internet.
* **Route Table (public-rt):** Defines the routing rules for network traffic within the VPC. Here, a route is configured to forward all internet-bound traffic from the Public Subnet (`0.0.0.0/0`) to the Internet Gateway.
* **EC2 Instance (my-ec2, t2.micro):** A virtual server in AWS. It uses the `t2.micro` instance type and the Amazon Linux 2 AMI. A public IP address is automatically assigned, making it accessible from the internet.

## 🛠️ What I did today 5/1
- I tried to make variable.tf.
- It worked to resolved errors.
- I learned that a lot of good point variable.tf.

## 📘 Note
- Keyword: variable depends unique

5/2
## Output Values


We defined output values in `output.tf` to make Terraform results reusable.  
For example, we output the EC2 public IP so we can connect via SSH easily.

1.私たちはアウトプットファイルを定義しています、テラフォームの結果を簡単にするために。
例えば、EC2パブリックIPと同じなのでSSH接続が簡単にできます。
(再利用可能)reusable (ために) to make （出力値）output values 
「Terraform の実行結果を再利用できるように、output.tf ファイルで出力値を定義しました。」
各単語の意味:

We: 私たちは
defined: 定義した (define の過去形)
output values: 出力値 (Terraform の実行後に得られる情報のこと)
in output.tf: output.tf の中で (Terraform の設定ファイルの名前)
to make: ～にするために
Terraform results: Terraform の結果 (Terraform のコマンドを実行した後に得られる情報や作成されたリソースの状態)
reusable: 再利用可能な

```hcl
output "ec2_public_ip" {
  value = aws_instance.example.public_ip
}

