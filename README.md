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

echo "🔄 Updating system packages..."
sudo dnf update -y

echo "📦 Installing Grafana Enterprise..."
GRAFANA_RPM="https://dl.grafana.com/enterprise/release/grafana-enterprise-12.0.2-1.x86_64.rpm"
sudo dnf install -y $GRAFANA_RPM

echo "✅ Grafana installed successfully."
grafana_version=$(grafana-server -v | head -n 1)
echo "🧾 Version: $grafana_version"

echo "🚀 Starting and enabling Grafana service..."
sudo systemctl enable --now grafana-server
sudo systemctl status grafana-server --no-pager

echo "🧑 Adding prometheus user (if not exists)..."
if id "prometheus" &>/dev/null; then
    echo "ℹ️ User 'prometheus' already exists."
else
    sudo useradd --no-create-home --shell /sbin/nologin prometheus
fi

echo "📁 Creating Prometheus directories..."
sudo mkdir -p /etc/prometheus /var/lib/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus /var/lib/prometheus

echo "⬇️ Downloading Prometheus..."
cd /tmp
PROM_VERSION="2.52.0"
wget -q https://github.com/prometheus/prometheus/releases/download/v$PROM_VERSION/prometheus-$PROM_VERSION.linux-amd64.tar.gz
tar -xzf prometheus-$PROM_VERSION.linux-amd64.tar.gz

echo "📂 Installing Prometheus binaries and config..."
cd prometheus-$PROM_VERSION.linux-amd64
sudo install -m 0755 prometheus promtool /usr/local/bin/
sudo cp -r consoles console_libraries /etc/prometheus/
sudo cp prometheus.yml /etc/prometheus/
sudo chown -R prometheus:prometheus /etc/prometheus /usr/local/bin/prometheus /usr/local/bin/promtool

echo "📝 Creating Prometheus systemd service..."
cat <<EOF | sudo tee /etc/systemd/system/prometheus.service
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

[Install]
WantedBy=multi-user.target
EOF

echo "🔄 Reloading systemd & starting Prometheus..."
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable --now prometheus
sudo systemctl status prometheus --no-pager

echo "✅ Prometheus setup complete!"


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

