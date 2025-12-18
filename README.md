# Flask & Express Deployment with Jenkins CI/CD

## Project Overview
This project demonstrates the deployment of a Flask backend and an Express frontend
on a single Amazon EC2 instance. It also includes a Jenkins-based CI/CD pipeline
to automate the deployment process.

The Express frontend provides a simple form UI, and on submission, the data is sent
to the Flask backend for processing.

---

## Architecture Overview
- Flask Backend running on port **5000**
- Express Frontend running on port **3000**
- Both applications deployed on the **same EC2 instance**
- Internal communication between frontend and backend using **localhost**
- Applications managed using **PM2 process manager**
- Public access via EC2 Public IP

---

## Technologies Used
- Python (Flask)
- Node.js (Express)
- PM2 (Process Manager)
- AWS EC2 (Ubuntu)
- Git & GitHub
- Jenkins (CI/CD)
- Linux

---

## Part 1: Deploy Flask & Express on a Single EC2 Instance

### EC2 Configuration
- EC2 instance launched with U

Install PM2 globally:
sudo npm install -g pm2

Run Flask backend using PM2:
pm2 start app.py --interpreter python3 --name flask-backend
pm2 save
PM2 Process Verification: pm2 status

or with: 
nohup python app.py > flask.log 2>&1 &

Backend URL:http://<EC2-PUBLIC-IP>:5000

Frontend Setup (Express)
cd frontend
npm install

Run Express frontend using PM2:
pm2 start server.js --name express-frontend
pm2 save

Frontend URL:http://<EC2-PUBLIC-IP>:3000

Part 2: Jenkins CI/CD Pipeline Setup and Automation

Jenkins was installed on the same EC2 instance where Flask and Express applications were deployed.
Java was installed as a prerequisite, followed by Jenkins installation.

Jenkins Installation Steps
Commands used to install Java and Jenkins:
sudo apt update
sudo apt install -y openjdk-17-jdk
java -version

Add Jenkins repository and install Jenkins:
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
/usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install -y jenkins

Start and enable Jenkins:
sudo systemctl start jenkins
sudo systemctl enable jenkins

Jenkins accessed using: http://<EC2-PUBLIC-IP>:8080

Jenkins Pipeline Creation

Two separate Jenkins pipelines were created:
One for Flask Backend
One for Express Frontend
Each pipeline pulls code from GitHub and installs required dependencies automatically.

Backend Pipeline (Flask)

Steps performed:
Jenkins connected to GitHub repository
Code pulled from main branch
Python virtual environment created
Backend dependencies installed
Commands executed in pipeline:
git clone https://github.com/Hashmi64/jenkins-flask-express.git
cd backend
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

Frontend Pipeline (Express)
Steps performed:
Code pulled from GitHub repository
Node.js dependencies installed using npm
Commands executed in pipeline:
git clone https://github.com/Hashmi64/jenkins-flask-express.git
cd frontend
npm install

GitHub Webhook Configuration (Automation)
GitHub was integrated with Jenkins to trigger pipelines automatically on every code push.
Steps performed in GitHub:
1.Opened GitHub repository settings
2.Navigated to Webhooks → Add Webhook
3.Configured webhook with the following details:
Webhook URL: http://<EC2-PUBLIC-IP>:8080/github-webhook/
Webhook settings:
Content type: application/json
Events: Push events
SSL verification: Disabled
Webhook set as Active

Jenkins Webhook Trigger Configuration
Steps performed in Jenkins:
1.Opened Jenkins job configuration
2.Enabled GitHub hook trigger for GITScm polling
3.Saved configuration

Automation Outcome
Jenkins pipelines run automatically on GitHub push
Manual builds also supported using “Build Now”
Backend and frontend dependency installation fully automated
CI/CD workflow validated with successful pipeline runs

