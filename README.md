
# Frontend Deployment on AWS EC2 with Nginx

This project demonstrates how to deploy a frontend application (built with a framework like React, Vue, or Angular) to an AWS EC2 Ubuntu server using Nginx.

## 🌐 Public URL

[http://agroexpress/](http://agroexpress.duckdns.org)

## 📸 Screenshot of the Deployed Page

![Screenshot](./public/agroexpress.png)

---

## 🛠️ Project Setup and Deployment Steps

### 1. 🚀 Launch EC2 Instance
- Log in to [AWS Console](https://aws.amazon.com/).
- Launch a new EC2 instance using Ubuntu 20.04 LTS.
- Select a security group that allows **SSH (port 22)**, **HTTP (port 80)**, and optionally **HTTPS (port 443)**.

### 2. 🔑 Connect to the EC2 Instance
```bash
ssh -i /path/to/your-key.pem ubuntu@13.48.147.215
```

### 3. 🧰 Update and Install Dependencies
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install nginx curl -y
```

### 4. 📦 Install Node.js (if needed for building frontend)
```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install nodejs -y
node -v
npm -v
```

### 5. 🏗️ Build Your Frontend Locally
From your project directory:
```bash
npm install
npm run build
```
This generates a `dist/` or `build/` folder.

### 6. 📤 Transfer Frontend Files to EC2
```bash
scp -i /path/to/your-key.pem -r dist/ ubuntu@13.48.147.215:/var/www/your-app
```

### 7. ⚙️ Configure Nginx
Create a new Nginx config:
```bash
sudo nano /etc/nginx/sites-available/your-app
```
Paste:
```nginx
server {
    listen 80;
    server_name _;

    root /var/www/your-app;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```
Enable site and restart Nginx:
```bash
sudo ln -s /etc/nginx/sites-available/your-app /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

### 8. ✅ Done!
Visit: [http://13.48.147.215/](http://13.48.147.215/)

---

## ✅ Optional: Enable UFW and Allow Nginx
```bash
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

## 🔒 No Domain? Skip SSL or Use Self-Signed Certificates

If you don’t have a domain, SSL via Certbot won’t work. You can either:
- Use HTTP only (default setup above)
- Use a free domain (e.g. from Freenom)
- Use a self-signed cert (not recommended for production)

---

## 💡 Tips
- Use `systemctl status nginx` to check service status.
- Upload new builds by replacing the contents in `/var/www/your-app`.
