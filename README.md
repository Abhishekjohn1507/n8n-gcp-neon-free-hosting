# 🧠 n8n on Google Cloud VM with Neon PostgreSQL (100% Free Setup)

This guide helps you deploy **n8n** on a **Google Cloud VM (Free Tier)** using **Neon.tech PostgreSQL** (also free). You won’t need to pay for Cloud SQL!

## ✅ Prerequisites

- A Google Cloud account with billing enabled
- A Neon.tech account
- Basic command line knowledge

## 🚀 Step-by-Step Setup

### 1. 🔧 Create a Neon PostgreSQL Database

- Go to https://neon.tech
- Create a new project
- Copy the connection string & credentials

### 2. 🖥️ Create Free VM on Google Cloud

1. Go to [Compute Engine](https://console.cloud.google.com/compute)
2. Click **"Create Instance"**
   - Machine type: `e2-micro` (free tier)
   - Region: `us-central1` or `us-west1` (required for free)
   - OS: **Ubuntu 22.04 LTS**
   - Allow HTTP & HTTPS traffic
3. SSH into the VM once created.

### 3. 🐳 Install Docker and Docker Compose

```bash
sudo apt update
sudo apt install docker.io docker-compose -y
sudo usermod -aG docker $USER
newgrp docker
```

### 4. 📦 Deploy n8n with Docker

```bash
git clone https://github.com/YOUR_USERNAME/n8n-gcp-neon-free-hosting.git
cd n8n-gcp-neon-free-hosting

cp .env.example .env
nano .env

docker-compose up -d
```

### 5. 🌐 Access n8n

Visit:
```
http://<YOUR_GCP_VM_EXTERNAL_IP>:5678
```

Log in with the username/password you set in `.env`.

## 🛡️ Optional: HTTPS Setup

You can secure your n8n using:
- A domain from Freenom
- Cloudflare DNS
- Caddy or Nginx + Certbot

## ✅ Advantages

- 💸 No cost for Google Cloud SQL
- 🆓 GCP VM & Neon DB are 100% free
- 🔐 Secured with basic auth
- 🔄 Easily upgradeable

## 🙌 Contributions

Pull requests welcome!

## 📜 License

MIT


---

## 🔐 Add a Domain Name with HTTPS using NGINX

### ✅ Prerequisites

- You have n8n running on your GCP VM
- You own a domain and its DNS A record points to your VM's external IP

### 1. Install NGINX and Certbot

```bash
sudo apt update
sudo apt install nginx certbot python3-certbot-nginx -y
```

### 2. Configure NGINX Reverse Proxy

```bash
sudo nano /etc/nginx/sites-available/n8n
```

Paste this config (replace your domain):

```nginx
server {
    listen 80;
    server_name n8n.yourdomain.com;

    location / {
        proxy_pass http://localhost:5678;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Enable the config:

```bash
sudo ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

### 3. Allow HTTP/HTTPS Ports in GCP Firewall

- Go to **GCP Console > VPC > Firewall Rules**
- Ensure ports 80 and 443 are allowed

### 4. Obtain SSL Certificate

```bash
sudo certbot --nginx -d n8n.yourdomain.com
```

### 5. Auto Renew SSL

```bash
sudo crontab -e
```

Add this line:

```bash
0 3 * * * certbot renew --quiet
```

Now your n8n instance is secure at: https://n8n.yourdomain.com
