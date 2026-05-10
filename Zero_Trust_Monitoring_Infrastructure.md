# Production Monitoring Stack using Grafana + Prometheus + Cloudflare Tunnel + Nginx

This project demonstrates a production-style monitoring setup using:

* Grafana
* Prometheus
* Nginx Reverse Proxy
* Cloudflare Tunnel
* Docker Compose
* AWS EC2 Ubuntu Server

The setup securely exposes Grafana and Prometheus dashboards without opening monitoring ports publicly.

---

# Architecture

<img width="1207" height="1303" alt="image" src="https://github.com/user-attachments/assets/38d3c100-d198-4add-9dac-1dff5e95e619" />


---

# Features

* Secure monitoring setup
* No public ports exposed for Grafana or Prometheus
* Cloudflare Tunnel based access
* Dockerized services
* Nginx reverse proxy
* Production-style deployment
* Auto-start services after reboot

---

# Prerequisites

Before starting, make sure you have:

* AWS EC2 Ubuntu instance
* Domain added in Cloudflare
* SSH access to EC2
* Cloudflare Tunnel installed and authenticated

---

# Step 1 — Install Docker

```bash
curl -fsSL https://get.docker.com | sh
```

Add ubuntu user to docker group:

```bash
sudo usermod -aG docker ubuntu
```

Apply changes:

```bash
newgrp docker
```

Verify:

```bash
docker ps
```

---

# Step 2 — Install Docker Compose Plugin

```bash
sudo apt install docker-compose-plugin -y
```

Verify:

```bash
docker compose version
```

---

# Step 3 — Create Monitoring Project

```bash
mkdir monitoring
cd monitoring
```

---

# Step 4 — Create Docker Compose File

Create file:

```bash
nano docker-compose.yml
```

Paste:

```yaml
services:

  prometheus:
    image: prom/prometheus
    container_name: prometheus
    restart: unless-stopped
    volumes:
      - ./prometheus:/etc/prometheus

  grafana:
    image: grafana/grafana-oss
    container_name: grafana
    restart: unless-stopped
    volumes:
      - grafana-storage:/var/lib/grafana

volumes:
  grafana-storage:
```

---

# Step 5 — Create Prometheus Configuration

Create directory:

```bash
mkdir prometheus
```

Create config file:

```bash
nano prometheus/prometheus.yml
```

Paste:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
```

---

# Step 6 — Start Containers

```bash
docker compose up -d
```

Verify:

```bash
docker ps
```

You should see:

* grafana
* prometheus

---

# Step 7 — Install Nginx

```bash
sudo apt install nginx -y
```

Enable nginx:

```bash
sudo systemctl enable nginx
```

Start nginx:

```bash
sudo systemctl start nginx
```

Check status:

```bash
systemctl status nginx
```

---

# Step 8 — Configure Nginx Reverse Proxy

Create nginx config:

```bash
sudo nano /etc/nginx/sites-available/monitoring
```

Paste:

```nginx
server {
    listen 80;
    server_name grafana.demodomain.fun;

    location / {
        proxy_pass http://localhost:3000;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

server {
    listen 80;
    server_name prometheus.demodomain.fun;

    location / {
        proxy_pass http://localhost:9090;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Enable configuration:

```bash
sudo ln -s /etc/nginx/sites-available/monitoring /etc/nginx/sites-enabled/
```

Test nginx:

```bash
sudo nginx -t
```

Reload nginx:

```bash
sudo systemctl reload nginx
```

---

# Step 9 — Install Cloudflared

Download package:

```bash
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
```

Install:

```bash
sudo dpkg -i cloudflared-linux-amd64.deb
```

Verify:

```bash
cloudflared --version
```

---

# Step 10 — Login to Cloudflare

```bash
cloudflared tunnel login
```

Complete browser authentication.

---

# Step 11 — Create Tunnel

```bash
cloudflared tunnel create prod-tunnel
```

Example output:

```text
Created tunnel prod-tunnel with id XXXXXXXX
```

---

# Step 12 — Create DNS Routes

Grafana DNS:

```bash
cloudflared tunnel route dns prod-tunnel grafana.demodomain.fun
```

Prometheus DNS:

```bash
cloudflared tunnel route dns prod-tunnel prometheus.demodomain.fun
```

---

# Step 13 — Configure Cloudflare Tunnel

Create config:

```bash
nano ~/.cloudflared/config.yml
```

Paste:

```yaml
tunnel: YOUR_TUNNEL_ID

credentials-file: /home/ubuntu/.cloudflared/YOUR_TUNNEL_ID.json

ingress:

  - hostname: grafana.demodomain.fun
    service: http://localhost:80

  - hostname: prometheus.demodomain.fun
    service: http://localhost:80

  - service: http_status:404
```

Replace:

* YOUR_TUNNEL_ID

with your actual tunnel ID.

---

# Step 14 — Install Cloudflare Tunnel Service

Install service:

```bash
sudo cloudflared service install
```

Enable service:

```bash
sudo systemctl enable cloudflared
```

Start service:

```bash
sudo systemctl start cloudflared
```

Check status:

```bash
systemctl status cloudflared
```

---

# Step 15 — Access Dashboards

Open:

```text
https://grafana.demodomain.fun
```

and

```text
https://prometheus.demodomain.fun
```

---

# Grafana Default Login

```text
Username: admin
Password: admin
```

You will be asked to change password after login.

---

# Step 16 — Add Prometheus Data Source in Grafana

Inside Grafana:

```text
Connections → Data Sources → Add Prometheus
```

URL:

```text
http://prometheus:9090
```

Save and Test.

---


