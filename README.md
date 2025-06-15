
# Frontend Deployment with Nginx on AWS EC2 (Ubuntu)

This guide details the steps taken to deploy a frontend application (React/Vue/Angular) on an **AWS EC2 Ubuntu instance** using **Nginx** as the web server.

---

## 🖥️ 1. Launch and Set Up AWS EC2 (Ubuntu)

1. Go to [AWS EC2 Console](https://console.aws.amazon.com/ec2/)
2. Launch an EC2 instance with:
   - **AMI**: Ubuntu Server (20.04 or later)
   - **Instance Type**: t2.micro (free tier eligible)
   - **Key Pair**: Create/download a `.pem` key
   - **Security Group**:
     - Allow SSH (port 22)
     - Allow HTTP (port 80)
     - (Optional) Allow HTTPS (port 443)

3. Connect to your instance:
```bash
ssh -i your-key.pem ubuntu@your-ec2-public-ip
```

---

## ⚙️ 2. Update Server Packages

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 🌐 3. Install and Configure Nginx

```bash
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

Allow Nginx through UFW:
```bash
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

Test by visiting: `http://your-ec2-public-ip`

---

## 🧰 4. Install Node.js and Build Frontend

Install Node.js (via NodeSource):
```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```

Build your frontend project:
```bash
npm install
npm run build  # outputs a /dist or /build folder
```

---

## 📂 5. Upload Frontend Files to Server

From your local machine:
```bash
scp -i your-key.pem -r dist/ ubuntu@your-ec2-public-ip:/var/www/your-app
```

---

## 📝 6. Configure Nginx to Serve Frontend

Create a config file:
```bash
sudo nano /etc/nginx/sites-available/your-app
```

Paste the following:

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

Enable the site:
```bash
sudo ln -s /etc/nginx/sites-available/your-app /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

Visit: `http://your-ec2-public-ip`

---

## 🔒 7. (Optional) SSL Setup

### Option A: Self-Signed Certificate
```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
 -keyout /etc/ssl/private/nginx-selfsigned.key \
 -out /etc/ssl/certs/nginx-selfsigned.crt
```

Update Nginx config to listen on 443 and include SSL paths.

### Option B: Use Free Domain + Let's Encrypt (if you have a domain)
```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx
```

---

## ✅ Deployment Complete

My Landing Page is now live and served through Nginx on my AWS EC2 instance.
