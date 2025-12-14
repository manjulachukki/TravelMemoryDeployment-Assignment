# TravelMemoryDeployment-Assignment
🏗️ Architecture Overview
End User (Browser / Mobile)
        |
     HTTP / HTTPS
        |
Application Load Balancer (Optional)
        |
     EC2 Instance
        |
     ┌─────────────┐
     │    Nginx    │  → Reverse Proxy & Static UI
     └──────┬──────┘
            |
     ┌──────▼──────┐
     │ Node.js App │  → Backend (PM2 Managed)
     └─────────────┘

     GitHub Repository
👉 https://github.com/UnpredictablePrashant/TravelMemory

Step 1: Create and Access EC2 Instance

Launch an EC2 instance

Attach a Security Group allowing ports 22, 80, 443

Login using PuTTY or SSH

ssh ec2-user@<EC2_PUBLIC_DNS>

Step 2: Update the System
Amazon Linux
sudo dnf update -y

Ubuntu
sudo apt update -y
Step 3: Install Nginx, Git, Firewall
Amazon Linux
sudo dnf install -y nginx git firewalld

Ubuntu
sudo apt install -y nginx git


Start and enable Nginx:

sudo systemctl enable --now nginx
Step 4: Open HTTP Port (Amazon Linux Only)
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload

🧪 Step 5: Test Default Nginx Page

Open browser:

http://<EC2_PUBLIC_IP>

🎨 Step 6: Replace Default Nginx Page
cd /usr/share/nginx/html
sudo tee index.html <<EOF
<!DOCTYPE html>
<html>
<head>
    <title>Welcome to My EC2 Website</title>
    <style>
        body { font-family: Arial; text-align: center; margin-top: 50px; }
        h1 { color: #2E8B57; }
    </style>
</head>
<body>
    <h1>Hello from Amazon Linux EC2!</h1>
    <p>This is a sample Nginx page deployed on AWS.</p>
</body>
</html>
EOF

📦 Step 7: Install Node.js, npm, and PM2
Amazon Linux (Recommended)
curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -
sudo dnf install -y nodejs

Ubuntu
sudo apt install -y nodejs npm


Verify:

node --version
npm --version


Install PM2:

sudo npm install -g pm2

📥 Step 8: Clone the Travel Memory Repository
git clone https://github.com/UnpredictablePrashant/TravelMemory
cd TravelMemory

⚙️ Step 9: Configure Environment Variables
sudo nano .env


Add required variables (example):

PORT=3000
NODE_ENV=production


Save and exit.

🏗️ Step 10: Install Dependencies & Build App
npm install
npm run build

🔁 Step 11: Configure Nginx Reverse Proxy

Create config file:

sudo nano /etc/nginx/sites-available/travel-memory


Paste:

server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}


Enable config:

sudo ln -s /etc/nginx/sites-available/travel-memory /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
sudo systemctl enable nginx

▶️ Step 12: Start Backend Using PM2
pm2 start index.js --name "travel-memory-backend"
pm2 save
pm2 restart travel-memory-backend


PM2 Management Commands:

pm2 stop travel-memory-backend
pm2 delete travel-memory-backend

🌍 Step 13: Access the Application

EC2 Public IP

http://<EC2_PUBLIC_IP>


EC2 DNS

http://ec2-98-93-49-85.compute-1.amazonaws.com


ALB DNS

http://Travel-Memory-App-ALB-mc-1708306676.us-east-1.elb.amazonaws.com


Custom Domain

http://manjulacharvik.online

🔐 Step 14: SSL Certificate (Optional)

Use AWS Certificate Manager (ACM) with ALB

OR use Let’s Encrypt + Certbot for Nginx

✅ Deployment Completed

Your Travel Memory Application is now fully deployed with:

Nginx Reverse Proxy

Node.js Backend

PM2 Process Management

Optional Load Balancer & SSL


