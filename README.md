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
  * **9090** (Prometheus)

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

## Install Prometheus 
// script
```
#!/bin/bash

set -e

# Ensure script runs as root
if [ "$EUID" -ne 0 ]; then
  echo "❌ Please run as root (use: sudo ./install.sh)"
  exit 1
fi

echo "🔄 Updating system packages..."
yum update -y

echo "📦 Installing Grafana Enterprise..."
yum install -y https://dl.grafana.com/enterprise/release/grafana-enterprise-12.0.2-1.x86_64.rpm

# Check Grafana version safely
if command -v grafana-server &> /dev/null; then
  echo "✅ Grafana installed successfully."
  grafana-server -v
else
  echo "❌ Grafana installation failed or binary not found!"
  exit 1
fi

echo "🚀 Starting and enabling Grafana service..."
systemctl daemon-reload
systemctl enable --now grafana-server

# Add prometheus user if it doesn’t exist
if id "prometheus" &>/dev/null; then
  echo "ℹ️ User 'prometheus' already exists."
else
  echo "👤 Adding user 'prometheus'..."
  useradd --no-create-home --shell /bin/false prometheus
fi

echo "📁 Creating directories..."
mkdir -p /etc/prometheus /var/lib/prometheus

echo "⬇️ Downloading Prometheus..."
cd /tmp
curl -LO https://github.com/prometheus/prometheus/releases/download/v2.52.0/prometheus-2.52.0.linux-amd64.tar.gz
tar xvf prometheus-2.52.0.linux-amd64.tar.gz
cd prometheus-2.52.0.linux-amd64

echo "📂 Copying binaries and configs..."
cp prometheus promtool /usr/local/bin/
cp -r consoles console_libraries /etc/prometheus
cp prometheus.yml /etc/prometheus

echo "🔐 Setting permissions..."
chown -R prometheus:prometheus /etc/prometheus /var/lib/prometheus
chown prometheus:prometheus /usr/local/bin/prometheus /usr/local/bin/promtool

echo "🧹 Cleaning up..."
cd /tmp
rm -rf prometheus-2.52.0.linux-amd64*
rm -f prometheus-2.52.0.linux-amd64.tar.gz

echo "⚙️ Creating Prometheus systemd service..."
tee /etc/systemd/system/prometheus.service > /dev/null <<EOF
[Unit]
Description=Prometheus Monitoring
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \\
  --config.file=/etc/prometheus/prometheus.yml \\
  --storage.tsdb.path=/var/lib/prometheus/ \\
  --web.console.templates=/etc/prometheus/consoles \\
  --web.console.libraries=/etc/prometheus/console_libraries
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

echo "🚀 Starting and enabling Prometheus..."
systemctl daemon-reload
systemctl enable --now prometheus

echo "✅ Prometheus status:"
systemctl status prometheus --no-pager

echo "🎉 Setup Complete!"
echo "📊 Access Grafana via: http://<your-ec2-public-ip>:3000 (default login: admin / admin)"

```



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

## 👨‍💻 Author

**Atul Kamble**

- 💼 [LinkedIn](https://www.linkedin.com/in/atuljkamble)
- 🐙 [GitHub](https://github.com/atulkamble)
- 🐦 [X](https://x.com/Atul_Kamble)
- 📷 [Instagram](https://www.instagram.com/atuljkamble)
- 🌐 [Website](https://www.atulkamble.in)


---

