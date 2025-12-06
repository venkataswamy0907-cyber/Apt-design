Live Domain Access: https://devopswithravi.xyz/

🚀 DevOps One-Click Deployment on AWS
This project deploys a fully automated AWS infrastructure using Terraform, including a private EC2 Auto Scaling architecture behind an Application Load Balancer (ALB).
It also includes a simple REST API running on port 8080 with / and /health endpoints.

📌 Architecture Overview
Client → ALB (Public Subnets) → Target Group → ASG → EC2 (Private Subnets)
|
NAT Gateway (Egress)
|
Internet Gateway


🖼 Architecture Components


🏗 AWS Resources Created
✔ VPC

<img width="1918" height="1078" alt="vpc" src="https://github.com/user-attachments/assets/a40bf2e6-6fe1-431a-87c2-b37804753576" />


✔ Subnets
<img width="1918" height="1078" alt="subnet" src="https://github.com/user-attachments/assets/fe3bc7e4-fead-4fbf-809f-b0b0c94fdebf" />



✔ Internet Gateway

<img width="1918" height="1078" alt="igw" src="https://github.com/user-attachments/assets/5b85b55a-06c2-4357-be5a-9c651a08c2e1" />

✔ NAT Gateway
<img width="1918" height="1078" alt="NAT" src="https://github.com/user-attachments/assets/2aafc90c-4286-4fb4-9cd4-9c3a3e6ade98" />


✔ Route Tables

<img width="1918" height="1077" alt="route table" src="https://github.com/user-attachments/assets/8b7dfa33-77a8-4662-a854-3b7539261937" />

⚙️ Load Balancer & Target Group
✔ ALB

<img width="1917" height="1078" alt="ALB" src="https://github.com/user-attachments/assets/2857f6eb-fce9-49a2-b107-b2b3107360f1" />

✔ ALB DNS Output
<img width="1918" height="1078" alt="output" src="https://github.com/user-attachments/assets/880b8a94-d712-407e-80e3-03e0eb5cd49a" />

✔ Target Group
<img width="1918" height="1078" alt="TG" src="https://github.com/user-attachments/assets/1d56afe0-ac5c-43a5-ac4f-ccaee626428d" />


✔ Auto Scaling Group

<img width="1918" height="1078" alt="auto scaling" src="https://github.com/user-attachments/assets/d0cd9031-844c-4941-924f-8c532542a93e" />

✔ EC2 (Private Instance — No Public IP)

<img width="1918" height="1078" alt="EC2" src="https://github.com/user-attachments/assets/027ead9e-8890-44af-a7f0-8291e43c47f8" />


🔒 Security Groups
✔ Secure Group
<img width="1918" height="1078" alt="Security_group" src="https://github.com/user-attachments/assets/59881476-8b9b-4eb6-833f-c3404a9a4754" />




🚀 Deployment Steps
1️⃣ Initialize
cd terraform
terraform init


Project Structure
├── app/
│   ├── app.js
│   └── package.json
├── terraform/
│   └── main.tf
├── scripts/
│   ├── deploy.sh
│   ├── destroy.sh
└── Screenshots/




🚀 Deployment Steps 

This guide explains how the infrastructure and application were deployed using Terraform and AWS services.

1️⃣ Prepare Your Project Structure

Ensure your folder structure looks like:

├── app/
│   └── app.js
│       package.json
├── terraform/
│   ├── main.tf
├── scripts/
│   ├── deploy.sh
│   ├── destroy.sh
└── Screenshots/

2️⃣ Configure AWS CLI
aws configure


Provide:

AWS Access Key

AWS Secret Key

Region → us-west-1

3️⃣ Initialize Terraform

Navigate to your terraform folder:

cd terraform/
terraform init

4️⃣ Deploy the Infrastructure

Run:

terraform apply -auto-approve


Terraform will automatically create:

✔ VPC (public + private subnets)
✔ Internet Gateway
✔ NAT Gateway
✔ ALB
✔ Target Group
✔ Auto Scaling Group
✔ Launch Template
✔ EC2 in private subnets
✔ IAM Role
✔ Security Groups

After deployment, Terraform shows the ALB DNS Name.

5️⃣ Test the Application Using ALB DNS (HTTP)

Copy the ALB DNS from Terraform output:

Example:

http://my-lb-123.us-west-1.elb.amazonaws.com/


Visit in browser:

Root Endpoint
http://<alb-dns>/


Expected output:

Server is running!

Health Endpoint
http://<alb-dns>/health


Expected output:

ok


⚠️ At this stage, the site will show Not Secure
This is normal because ALB is still using HTTP.

🔐 Optional (Recommended): Enable HTTPS + Custom Domain

You enabled HTTPS using Route 53 + ACM, so write these steps:

6️⃣ Create Hosted Zone in Route 53

Open Route 53

Create Public Hosted Zone

Enter your domain (example: devopswithravi.xyz)

Update your domain registrar with the NS records

7️⃣ Request SSL Certificate from ACM

Open ACM (us-east-1)

Request certificate for:

devopswithravi.xyz
*.devopswithravi.xyz


Choose DNS validation

ACM provides CNAME records → Add them to Route 53

Certificate becomes Issued

8️⃣ Enable HTTPS on ALB

Modify ALB listener:

Add listener HTTPS (443)

Attach your ACM certificate

Forward to target group

9️⃣ Route Domain to ALB

In Route 53:

Create A – Alias record

Alias to → Your Application Load Balancer

Save

🔟 Access Your Secure Application
https://devopswithravi.xyz/

https://devopswithravi.xyz/health


✔ Secure HTTPS
✔ Valid SSL
✔ Load Balancer → Private EC2 (via ASG)


🧪 Testing Steps

After your infrastructure is deployed and the application is running behind the Load Balancer, follow the steps below to test that everything works correctly.

1️⃣ Verify the ALB URL or Custom Domain

If you're using your ALB DNS:

http://<your-alb-dns>/


If you configured Route 53 + HTTPS (recommended):

https://devopswithravi.xyz/


This should load your application.

2️⃣ Test the Root Endpoint ( / )

Open your browser or run:

curl https://devopswithravi.xyz/


Expected output:

Server is running!


Confirm this screenshot:
Screenshots/LoadbalacerDNS-Ouput.jpg

3️⃣ Test the Health Endpoint ( /health )

Test via browser:

https://devopswithravi.xyz/health


or:

curl https://devopswithravi.xyz/health


Expected output:

ok


Corresponding screenshot:
Screenshots/Loadbalacer-output health.jpg

4️⃣ Verify ALB Health Checks

Go to AWS Console → EC2 → Target Groups

Select your target group

Check Registered Targets

Ensure health is:

healthy


Screenshot:
Screenshots/TargetGroup.jpg

5️⃣ Verify Private EC2 Instances (No Public Access)

Go to EC2 Console

Check your Auto Scaling Group

Instances must have:

Private IP only

No public IPv4

Running inside private subnets

Screenshot:
Screenshots/EC2.jpg

Try SSH — you should NOT be able to access it (for security reasons).

6️⃣ Validate Load Balancer DNS → EC2 Traffic Flow

This confirms:

✔ ALB is routing traffic
✔ ASG launched instances
✔ User-data bootstrapped the application
✔ NAT gateway allows outbound internet access for app dependencies

7️⃣ Use CloudWatch Logs (Optional)

If EC2 IAM Role includes CloudWatch logging:

/var/log/cloud-init.log
/var/log/messages
application logs from app.js


All logs should show startup messages like:

Server is running!

8️⃣ Confirm Auto Scaling Works (Optional)

To simulate:

Increase CPU load

Or use Update desired capacity in ASG

New instance should deploy automatically and become healthy under ALB.

✅ Testing Complete

Once all tests pass, your deployment is verified end-to-end.

🧹 Teardown Steps

Follow these steps to safely remove all AWS resources and avoid unnecessary charges.

1️⃣ Navigate to the Scripts Directory
cd scripts/

2️⃣ Run the Destroy Script

Make the script executable (if not already):

chmod +x destroy.sh


Run:

./destroy.sh


This script will:

✔ Run terraform destroy -auto-approve
✔ Delete all infrastructure created by Terraform
✔ Remove:

VPC

Public & private subnets

Internet + NAT Gateway

Route tables

Application Load Balancer

Target group

Auto Scaling Group

Launch Template

Security groups

IAM roles

EC2 instances

Any other Terraform-managed resources

3️⃣ (Optional) Delete Route 53 Hosted Zone & Domain Records

If you used a custom domain:

Go to Route 53 → Hosted Zones

Select your hosted zone (devopswithravi.xyz)

Delete A records, CNAMEs, ALIAS records

Finally delete the hosted zone (cannot delete until empty)

4️⃣ (Optional) Delete ACM Certificate

If you're not using your domain elsewhere:

Go to ACM

Find your certificate

Delete it

5️⃣ Verify All Resources Are Removed

In AWS Console, verify:

No Load Balancers

No Auto Scaling Groups

No EC2 instances

No NAT Gateway (expensive!)

No VPC associated resources

No Elastic IP

✅ Teardown Complete

Your AWS environment is now fully cleaned and free from ongoing charges.
