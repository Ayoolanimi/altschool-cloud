# Altschool Web Project – Dynamic Resume Application

This project is a dynamic, Node.js-powered web application hosted on an AWS EC2 instance, using **Nginx** as a reverse proxy for professional-grade deployment. It showcases HTML, CSS, and JavaScript content served via an Express backend.

---

## 🌐 Live on EC2

Deployed on: `http://<your-ec2-public-ip>`

---

## 🛠️ Tech Stack

- **Cloud Provider**: AWS EC2 (Ubuntu 20.04)
- **Web Server**: NGINX (Reverse proxy)
- **Runtime**: Node.js (v18+)
- **Backend Framework**: Express.js
- **Deployment Tool**: PM2 (for process management)
- **Security**: UFW (Uncomplicated Firewall)

---

## 🚀 Project Setup Steps

### 1. Launch EC2 Instance
- Provider: AWS
- AMI: Ubuntu 20.04 LTS
- Open ports: 22 (SSH), 80 (HTTP), optionally 3000 or 8080
- Download your `aws.pem` key file securely

### 2. Connect to the EC2 Instance

```bash
ssh -i "path/to/aws.pem" ubuntu@your-ec2-ip
```
### 3. Connect to the EC2 InstanceInstall Dependencies on the Server

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install nginx nodejs npm git -y
sudo npm install -g pm2
```
### 4. Prepare Project Directory

```bash
mkdir -p ~/altschool-web/public
cd ~/altschool-web
```

### 5. Upload Files from Local Machine to Server

```bash
scp -i "path/to/aws.pem" index.html ubuntu@your-ec2-ip:/home/ubuntu/altschool-web/public/
scp -i "path/to/aws.pem" nicepage.css ubuntu@your-ec2-ip:/home/ubuntu/altschool-web/public/
scp -i "path/to/aws.pem" nicepage.js ubuntu@your-ec2-ip:/home/ubuntu/altschool-web/public/
```

### 6. Initialize Node.js Project

```bash
cd ~/altschool-web
npm init -y
npm install express
```

### 7. Create server.js

```js
// ~/altschool-web/server.js
const express = require('express');
const path = require('path');

const app = express();
const port = process.env.PORT || 3000;

app.use(express.static(path.join(__dirname, 'public')));

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

### 8. Run App with PM2
```bash
pm2 start server.js --name altschool-app
pm2 startup
pm2 save
```

### 9. Configure NGINX as Reverse Proxy

```bash
sudo nano /etc/nginx/sites-available/altschool-web
```
Then,
```config
server {
    listen 80;
    listen 443;
    server_name ec2-ip;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```
Then,
```enable the config
sudo ln -s /etc/nginx/sites-available/altschool-web /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```


👨‍💻 Author
Oluwatoyin Ajetunmobi

