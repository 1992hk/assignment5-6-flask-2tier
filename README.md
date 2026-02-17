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
docker build -t 783764582691.dkr.ecr.ap-south-1.amazonaws.com/nov25-class5:2.0 .

===============================================================

Step 7: Now need to Push the image to ECR by using AWS CLI to login to AWS

Now i have logged inside AWS by using CLI

Command: aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 879381241087.dkr.ecr.ap-south-1.amazonaws.com

Now i need to push the image to ECR: IN MY DEMO I AM NOT USING AWS CLI in EC2 i am installing CLI

apt install awscli -y
aws --version

No Inside Ec2 i have run these commands and gave Secret and Access Key

aws configure
Username: DevOps-bootcamp
Access Key: 
Secret Access Key: 
Region: ap-south-1
Output format: json

docker build -t 783764582691.dkr.ecr.ap-south-1.amazonaws.com/nov25-class5:2.0 .

aws ecr get-login-password --region ap-south-1 | \
docker login --username AWS --password-stdin 783764582691.dkr.ecr.ap-south-1.amazonaws.com

docker push 783764582691.dkr.ecr.ap-south-1.amazonaws.com/assignment5-flask-app:2.0

Now i see the image is puched to ECR

============================================================

Step 7: Login to ECR from EC2

aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 783764582691.dkr.ecr.ap-south-1.amazonaws.com

============================================================

Step 8:Test Database Connectivity

docker run --rm -it postgres:16 psql -h assignment5-db.cl0u0eeayj3i.ap-south-1.rds.amazonaws.com -p 5432 -U postgres -d postgres

After gave the above command its asked postgres password and then exit from it

============================================================

Step 9: Run Application Container

syntax: postgresql://<username>:<password>@<host>:<port>/<database>

export DATABASE_URL=postgresql://postgres:password01D@assignment5-db.cl0u0eeayj3i.ap-south-1.rds.amazonaws.com:5432/postgres

docker run -e DATABASE_URL=$DATABASE_URL -td -p 5000:5000 783764582691.dkr.ecr.ap-south-1.amazonaws.com/assignment5-flask-app:2.0

curl http://localhost:5000 ===> http://<EC2_PUBLIC_IP>:5000 ===>http://13.200.243.35:5000

After running the above command got this output

root@ip-172-31-8-194:~/assignment5# curl http://localhost:5000
Flask 2-Tier App Running with HTTPS 🚀root@ip-172-31-8-194:~/assignment5#

============================================================

