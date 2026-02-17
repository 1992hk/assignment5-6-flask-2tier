Step 1:

Created one ubuntu Ec2 and Editted the Security group:

Allow 22 (SSH)
Allow 80 (HTTP)
Allow 443 (HTTPS)

Database - PostgreSQL - Port: 5432

===============================================================

Step2: Create IAM User:

Go to AWS Console → IAM → Users → Create User
Name: nov25-bootcamp
Don't check console access
Attach policy: AdministratorAccess (for learning - use restricted in production)
Create access key → CLI
Save Access Key ID and Secret Access Key

===============================================================

Step 3:
Create PostgreSQL RDS
Go to AWS Console → RDS → Create Database

Settings:
Engine → PostgreSQL
Version → Default
Template → Free tier
DB instance ID → assignment5-db
Master username → postgres
Password → (save it)

Connectivity:
Public access → No (recommended)
VPC → Default

After creation:
Copy the Endpoint: assignment5-db.cl0u0eeayj3i.ap-south-1.rds.amazonaws.com

===============================================================

Step 4:

Configure RDS Security Group

Go to:

EC2 → Security Groups → Select RDS SG

Add inbound rule:

Type: PostgreSQL
Port: 5432
Source: EC2 Security Group (NOT 0.0.0.0)

Datacase Details:

assignment5-db.cl0u0eeayj3i.ap-south-1.rds.amazonaws.com

Port: 5432
Username: postgres
Password: password01
Database: postgres

===============================================================

Step 5: install docker and Docker compose

sudo yum update -y
sudo yum install docker -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ec2-user

sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

docker --version
docker-compose --version

===============================================================

Step 6: Open AWS ECR and created one repository: assignment5-flask-app
URL: 783764582691.dkr.ecr.ap-south-1.amazonaws.com/assignment5-flask-app

Build Docker Image
docker build -t 879381241087.dkr.ecr.ap-south-1.amazonaws.com/nov25-class5:2.0 .

===============================================================

Step 7: Now need to Push the image to ECR by using AWS CLI to login to AWS

Now i have logged inside AWS by using CLI

Command: aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 879381241087.dkr.ecr.ap-south-1.amazonaws.com

Now i need to push the image to ECR:


