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

# 🌐 Step 5 — Install Nginx

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
 **And Also Install systemd**

```bash
# Install systemd service
sudo cloudflared service install

```

```bash
# Start tunnel service
sudo systemctl start cloudflared
```

```bash
# Enable auto start after reboot
sudo systemctl enable cloudflared
```


```bash
# Check status
sudo systemctl status cloudflared
```
 
---

# ☁️ Step 6 — Add Domain to Cloudflare

Login to Cloudflare.

**Path : CloudFlare > Dashboard > Domain > Add Domain** 

<img width="1866" height="811" alt="image" src="https://github.com/user-attachments/assets/f6944159-331a-4171-881e-94d9ebd8b534" />


Add your domain:

```text
Add Your Doamin
```
**After This Click on Import DNS Records**

Cloudflare provides nameservers like:

```text
<Your DNS Name>.com
<Your DNS Name>.com
```



<img width="1890" height="860" alt="image" src="https://github.com/user-attachments/assets/7db2644e-5995-45d2-8584-0748079e3024" />

---


<img width="1881" height="880" alt="image" src="https://github.com/user-attachments/assets/986d56ee-9214-4c78-87ba-70d0dc080e7e" />



---

# 🌍 Step 7 — Update Nameservers in Domain Provider

Go to your domain provider.

Example:

* Hostinger
* GoDaddy
* Namecheap

Replace old nameservers with Cloudflare nameservers.
<img width="3145" height="1665" alt="image" src="https://github.com/user-attachments/assets/0f49a023-df13-45b6-8bfc-3bdd35845b3d" />

**after this wait 5 min** 



---

# ✅ Step 10 — Verify Cloudflare Activation

In Cloudflare dashboard:

```text
Domain Status → Active
```


<img width="1886" height="828" alt="image" src="https://github.com/user-attachments/assets/876510ee-2ffa-4a90-abf1-68f96975dda2" />

**or check using dig NS <Your Doamin Name>**

<img width="2419" height="1310" alt="image" src="https://github.com/user-attachments/assets/5e062632-51f4-473f-af53-522c77f9de29" />

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

<img width="827" height="47" alt="590097620-bff70e6d-b89f-46da-b6e9-597397d9be98" src="https://github.com/user-attachments/assets/25c584d1-9908-4241-aa50-56de0a4357d2" />

---

<img width="945" height="411" alt="590097288-c83ba327-6265-45d1-a862-9fc6081abb02" src="https://github.com/user-attachments/assets/6125d568-21d3-41a6-872c-e318ba2179d0" />


**It will Provide Your URL Open That into web browser to authorise**

Select your domain and Click On Authorise.

Successful login creates:

<img width="1876" height="871" alt="Screenshot (2140)" src="https://github.com/user-attachments/assets/2d832cc2-4273-4b98-9f89-32e05af388d0" />

---


<img width="954" height="400" alt="590097443-eaabd7f1-7336-4d5c-9f2d-a52c9a8b80de" src="https://github.com/user-attachments/assets/1cb0c8ba-cbd4-4190-8e10-f906e8cc5602" />

**It will Download certificate on this location**
```text
/home/ubuntu/.cloudflared/cert.pem
```




---

# 🚇 Step 13 — Create Tunnel

Create tunnel:

```bash
cloudflared tunnel create <Tunnel Name >
```


<img width="1875" height="139" alt="590097620-bff70e6d-b89f-46da-b6e9-597397d9be98 (1)" src="https://github.com/user-attachments/assets/96664cb2-fb8b-4903-9cc5-374fe25fc814" />

Example output:

```text
Created tunnel prod-tunnel with id:
<Tunnel ID Number>
```

Credentials file created:

```text
/home/ubuntu/.cloudflared/TUNNEL_ID.json
```


<img width="1898" height="953" alt="590097657-d24c4ef3-abb2-43cb-b91f-1fa58c09803f" src="https://github.com/user-attachments/assets/4ba72f4d-90db-4f95-a38b-0278fca4d80e" />


---

# 🌐 Step 14 — Create DNS Route



IMPORTANT:

Root domain may already contain DNS records.

Wrong:

```bash
cloudflared tunnel route dns prod-tunnel <Your Doamin>
```

Correct:

```bash
cloudflared tunnel route dns prod-tunnel <app.Your Doamin>
```

Successful output:

```text
Added CNAME app.Your Doamin
```
<img width="849" height="335" alt="590097723-d4ae4ffe-0159-42f2-8c91-b94c3897cb51" src="https://github.com/user-attachments/assets/bbc95987-7cb4-4aca-b672-7337c23839fc" />

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
  - hostname: app.Your Doamin
    service: http://localhost:80

  - service: http_status:404
```

Replace:

* YOUR_TUNNEL_ID
* domain name

---

# ▶️ Step 16 — Run Tunnel in Background Because If Machine Restart Tunnel will expire 

Start tunnel:

```bash
nohup cloudflared tunnel run <Your Tunnel Name> > tunnel.log 2>&1 &
```

Successful logs:

```text
Registered tunnel connection
```

---

# 🌍 Step 17 — Access Website
<img width="1651" height="915" alt="Screenshot (2145)" src="https://github.com/user-attachments/assets/16efc69c-2439-47f3-bf75-9b00ae7be723" />


Open browser:

```text
https://app.Your Doamin
```

Website should load successfully.





