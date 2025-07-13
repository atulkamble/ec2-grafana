Instance type - t3.medium | amazon linux 


sudo yum update -y

sudo yum install -y https://dl.grafana.com/enterprise/release/grafana-enterprise-12.0.2-1.x86_64.rpm
grafana --version
sudo systemctl start grafana-server
sudo systemctl enable grafana-server


sudo useradd --no-create-home --shell /bin/false prometheus
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
cd /tmp

curl -LO https://github.com/prometheus/prometheus/releases/download/v2.52.0/prometheus-2.52.0.linux-amd64.tar.gz
tar xvf prometheus-2.52.0.linux-amd64.tar.gz

cd prometheus-2.52.0.linux-amd64

sudo cp prometheus /usr/local/bin/
sudo cp promtool /usr/local/bin/
sudo cp -r consoles /etc/prometheus
sudo cp -r console_libraries /etc/prometheus
sudo cp prometheus.yml /etc/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus /var/lib/prometheus
sudo chown prometheus:prometheus /usr/local/bin/prometheus /usr/local/bin/promtool


sudo nano /etc/systemd/system/prometheus.service

```
[Unit] 
 Description=Prometheus Monitoring 
 Wants=network-online.target 
 After=network-online.target 
  
 [Service] 
 User=prometheus 
 Group=prometheus 
 Type=simple 
 ExecStart=/usr/local/bin/prometheus \ 
   --config.file=/etc/prometheus/prometheus.yml \ 
   --storage.tsdb.path=/var/lib/prometheus/ \ 
   --web.console.templates=/etc/prometheus/consoles \ 
   --web.console.libraries=/etc/prometheus/console_libraries 
  
 [Install] 
 WantedBy=multi-user.target
```

sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
sudo systemctl status prometheus




// script

sudo yum update -y
sudo yum install -y https://dl.grafana.com/enterprise/release/grafana-enterprise-12.0.2-1.x86_64.rpm
grafana --version
sudo systemctl start grafana-server
sudo systemctl enable grafana-server

sudo useradd --no-create-home --shell /bin/false prometheus
sudo mkdir -p /etc/prometheus /var/lib/prometheus
cd /tmp
curl -LO https://github.com/prometheus/prometheus/releases/download/v2.52.0/prometheus-2.52.0.linux-amd64.tar.gz
tar xvf prometheus-2.52.0.linux-amd64.tar.gz
cd prometheus-2.52.0.linux-amd64
sudo cp prometheus promtool /usr/local/bin/
sudo cp -r consoles console_libraries /etc/prometheus
sudo cp prometheus.yml /etc/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus /var/lib/prometheus
sudo chown prometheus:prometheus /usr/local/bin/prometheus /usr/local/bin/promtool
cd /tmp && rm -rf prometheus-2.52.0.linux-amd64*

sudo tee /etc/systemd/system/prometheus.service > /dev/null <<EOF
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

sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
sudo systemctl status prometheus





























sudo yum install tree -y 


scp -i prom.pem /Users/atul/Downloads/prometheus-3.4.2.linux-amd64.tar.gz ec2-user@ec2-34-201-73-22.compute-1.amazonaws.com://home/ec2-user

sudo tar xvfz prometheus-*.tar.gz




sudo mkdir /etc/prometheus /var/lib/prometheus

wget https://github.com/prometheus/prometheus/releases/download/v3.4.2/prometheus-3.4.2.linux-amd64.tar.gz

sudo tar -xvf prometheus-3.4.2.linux-amd64.tar.gz

sudo cp prometheus-3.4.2.linux-amd64/prometheus /usr/local/bin/

sudo cp prometheus-3.4.2.linux-amd64/promtool /usr/local/bin/

sudo cp prometheus-3.4.2.linux-amd64/prometheus.yml /etc/prometheus/

sudo cp prometheus-3.4.2.linux-amd64/prometheus.yml /etc/prometheus/

sudo cp prometheus-3.4.2.linux-amd64/rules.yml /etc/prometheus/

sudo cd /etc/systemd/system

sudo touch prometheus.service

sudo nano prometheus.service

```
[Unit]
Description=Prometheus Monitoring
After=network.target

[Service]
User=prometheus
ExecStart=/usr/local/bin/prometheus \\
  --config.file=/etc/prometheus/prometheus.yml \\
  --storage.tsdb.path=/var/lib/prometheus/ \\
  --web.console.templates=/etc/prometheus/consoles \\
  --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

sudo useradd --no-create-home --shell /bin/false prometheus

sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus
sudo chown -R prometheus:prometheus /var/lib/prometheus

sudo systemctl daemon-reload
sudo systemctl start prometheus
sudo systemctl enable prometheus


// node exporter

sudo useradd --no-create-home --shell /bin/false node_exporter

sudo wget https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz

sudo tar -xvf node_exporter-1.9.1.linux-amd64.tar.gz

sudo cd /etc/systemd/system/

sudo touch node_exporter.service

sudo nano node_exporter.service

```
Unit]
Description=Node Exporter
After=network.target

[Service]
User=node_exporter
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```


sudo systemctl daemon-reload

sudo systemctl start node_exporter

sudo systemctl enable node_exporter



