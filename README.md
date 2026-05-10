# 🚀 Production Ready Cloudflare Tunnel Setup on AWS EC2

---

# 🏗️ Architecture

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/08e15ca7-f739-4f65-a10d-872f6aa3bbb6" />


---

# 🛠️ Technologies Used

| Technology        | Purpose               |
| ----------------- | --------------------- |
| AWS EC2           | Cloud Server          |
| Ubuntu 24.04      | Operating System      |
| Nginx             | Web Server            |
| Cloudflare Tunnel | Secure Access         |
| Cloudflare DNS    | Domain Routing        |
| Linux             | Server Administration |

---

# 📋 Prerequisites

Before starting:

* AWS Account
* Cloudflare Account
* Domain Name
* SSH Client
* Basic Linux Knowledge
* Nginx 


---

# ☁️ Step 1 — Launch AWS EC2 Instance

Login to AWS Console.

Go to:

```text
EC2 → Launch Instance
```

Choose:

| Setting        | Value               |
| -------------- | ------------------- |
| AMI            | Ubuntu 24.04        |
| Instance Type  | t2.micro / t3.micro |
| Key Pair       | Create new          |
| Storage        | 20GB                |
| Security Group | Allow SSH only      |

---

# 🔐 Step 2 — Configure Security Group

Allow ONLY:

| Type | Port |
| ---- | ---- |
| SSH  | 22   |

DO NOT OPEN:

```text
80
443
3000
8080
9090
```

Cloudflare Tunnel handles public traffic securely.

---

# 🔑 Step 3 — Connect to EC2

Use SSH:

```bash
ssh -i your-key.pem ubuntu@YOUR_PUBLIC_IP
```

---

# 📦 Step 4 — Update Server

Update packages:

```bash
sudo apt update && sudo apt upgrade -y
```

Install utilities:

```bash
sudo apt install curl wget unzip nano -y
```

---

# 📁 Step 5 — Create Sample Application

Create directory:

```bash
mkdir mysite
cd mysite
```

Create HTML page:

```bash
nano index.html
```

Add:

```html
<h1>Cloudflare Tunnel Working</h1>

<p>Hello from EC2 localhost application</p>
```

Save:

```text
CTRL + O
ENTER
CTRL + X
```

---

# 🌐 Step 6 — Install Nginx

Install:

```bash
sudo apt install nginx -y
```

Enable service:

```bash
sudo systemctl enable nginx
```

Start service:

```bash
sudo systemctl start nginx
```

Check status:

```bash
systemctl status nginx
```

Expected:

```text
active (running)
```

---

# 📂 Step 7 — Configure Nginx Website

Remove default HTML:

```bash
sudo rm -rf /var/www/html/*
```

Copy application files:

```bash
sudo cp ~/mysite/index.html /var/www/html/
```

Restart Nginx:

```bash
sudo systemctl restart nginx
```

Test locally:

```bash
curl localhost
```

Expected output:

```html
<h1>Cloudflare Tunnel Working</h1>
```

---

# ☁️ Step 8 — Add Domain to Cloudflare

Login to Cloudflare.

Add your domain:

```text
demodomain.fun
```

Cloudflare provides nameservers like:

```text
marge.ns.cloudflare.com
rex.ns.cloudflare.com
```

---

# 🌍 Step 9 — Update Nameservers in Domain Provider

Go to your domain provider.

Example:

* Hostinger
* GoDaddy
* Namecheap

Replace old nameservers with Cloudflare nameservers.

Wait for DNS propagation.

---

# ✅ Step 10 — Verify Cloudflare Activation

In Cloudflare dashboard:

```text
Domain Status → Active
```

---

# 🚇 Step 11 — Install Cloudflared

Download package:

```bash
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
```

Install:

```bash
sudo dpkg -i cloudflared-linux-amd64.deb
```

Verify installation:

```bash
cloudflared --version
```

---

# 🔐 Step 12 — Authenticate Cloudflare Tunnel

Run:

```bash
cloudflared tunnel login
```

Browser opens automatically.

Login with Cloudflare account.

Select your domain.

Successful login creates:

```text
/home/ubuntu/.cloudflared/cert.pem
```

---

# 🚇 Step 13 — Create Tunnel

Create tunnel:

```bash
cloudflared tunnel create prod-tunnel
```

Example output:

```text
Created tunnel prod-tunnel with id:
fb4b1406-fc69-4410-8f12-ebef478eb529
```

Credentials file created:

```text
/home/ubuntu/.cloudflared/TUNNEL_ID.json
```

---

# 🌐 Step 14 — Create DNS Route

IMPORTANT:

Root domain may already contain DNS records.

Wrong:

```bash
cloudflared tunnel route dns prod-tunnel demodomain.fun
```

Correct:

```bash
cloudflared tunnel route dns prod-tunnel app.demodomain.fun
```

Successful output:

```text
Added CNAME app.demodomain.fun
```

---

# ⚙️ Step 15 — Configure Tunnel

Create config:

```bash
nano ~/.cloudflared/config.yml
```

Add:

```yaml
tunnel: YOUR_TUNNEL_ID

credentials-file: /home/ubuntu/.cloudflared/YOUR_TUNNEL_ID.json

ingress:
  - hostname: app.demodomain.fun
    service: http://localhost:80

  - service: http_status:404
```

Replace:

* YOUR_TUNNEL_ID
* domain name

---

# ▶️ Step 16 — Run Tunnel

Start tunnel:

```bash
cloudflared tunnel run prod-tunnel
```

Successful logs:

```text
Registered tunnel connection
```

---

# 🌍 Step 17 — Access Website

Open browser:

```text
https://app.demodomain.fun
```

Website should load successfully.

---

# 🔒 Security Benefits

| Traditional Hosting    | Cloudflare Tunnel    |
| ---------------------- | -------------------- |
| Open public ports      | No inbound ports     |
| Public server exposure | Hidden origin IP     |
| Manual SSL setup       | Automatic HTTPS      |
| Direct attack surface  | Cloudflare protected |

---

# 📚 Key Concepts Learned

During this project:

* AWS EC2
* Linux Administration
* Nginx Configuration
* DNS Management
* Cloudflare Tunnel
* Reverse Proxy Concepts
* Secure Networking
* Production Architecture
* HTTPS Routing

---

# 📈 Future Improvements

Possible enhancements:

* Grafana Monitoring
* Prometheus Metrics
* Docker Integration
* Kubernetes Deployment
* Zero Trust Authentication
* CI/CD Integration
* WAF Rules
* Rate Limiting

---

# 🧠 Conclusion

This project demonstrates a modern and secure way to expose applications publicly without directly exposing server ports.

Instead of:

```text
Internet → Open Ports → Server
```

we use:

```text
Server → Secure Tunnel → Cloudflare
```

This approach improves:

* Security
* Scalability
* Maintainability
* Production readiness

A strong practical project for DevOps and Cloud Engineering 🚀
