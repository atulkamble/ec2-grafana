# 📊 EC2 Grafana Deployment

This project provides step-by-step instructions to deploy **Grafana Enterprise** on an AWS EC2 instance.

---

## 📦 Prerequisites

* AWS Account with permissions to launch EC2 instances
* SSH key pair for EC2 access
* Security Group allowing required ports:

  * **22** (SSH)
  * **80** (HTTP)
  * **443** (HTTPS)
  * **3000** (Grafana)

---

## 🚀 EC2 Instance Launch & Setup

### 1️⃣ Launch EC2 Instance

* Choose **Amazon Linux 2 AMI**
* Instance type: `t2.micro` (or higher)
* Attach the following **Security Group** rules:

| Type       | Protocol | Port Range | Source    |
| ---------- | -------- | ---------- | --------- |
| SSH        | TCP      | 22         | Your IP   |
| HTTP       | TCP      | 80         | 0.0.0.0/0 |
| HTTPS      | TCP      | 443        | 0.0.0.0/0 |
| Custom TCP | TCP      | 3000       | 0.0.0.0/0 |

---

### 2️⃣ Connect to EC2 via SSH

```bash
ssh -i "your-key.pem" ec2-user@your-ec2-public-ip
```

---

## 📥 Install Grafana Enterprise

Run the following commands on the EC2 instance:

```bash
sudo yum install -y https://dl.grafana.com/enterprise/release/grafana-enterprise-12.0.2-1.x86_64.rpm
grafana --version
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

---

## 🌐 Access Grafana

Open your browser and visit:

```
http://your-ec2-public-ip:3000
```

* **Username:** `admin`
* **Password:** `admin` (change after first login)

---

## 📓 Notes

* Ensure your **Security Group** allows inbound traffic on port **3000**.
* Replace `your-ec2-public-ip` with the actual Public IPv4 address of your EC2 instance.

---

## 📷 Example

![Grafana Login](screenshots/grafana-login.png)

---

## 📂 Project Structure

```
ec2-grafana/
├── README.md
└── screenshots/
    └── grafana-login.png
```

---

## 📌 Author

**Atul Kamble**
Cloud Solutions Architect & Trainer

---

