# Prometheus, Grafana, and Node Exporter Setup Guide

## Server Details
- **Server 1 (Prometheus & Grafana)**: 192.168.10.10
- **Server 2 (Node Exporter)**: 192.168.10.11

---

## 1. Prometheus Installation and Configuration

Let's start by installing Prometheus on **Server 1**.

### Step 1: Update and Install Required Packages
```bash
~# sudo apt update && sudo apt upgrade -y
~# cd /tmp
~# curl -LO https://github.com/prometheus/prometheus/releases/download/v2.47.0/prometheus-2.47.0.linux-amd64.tar.gz
~# tar -xvf prometheus-2.47.0.linux-amd64.tar.gz
~# sudo mv prometheus-2.47.0.linux-amd64 /usr/local/prometheus
```
### Step 2: Create a User for Prometheus
```bash
sudo useradd --no-create-home --shell /bin/false prometheus
sudo chown -R prometheus:prometheus /usr/local/prometheus
```

### Step 3: Create a Systemd Service for Prometheus
```bash
sudo nano /etc/systemd/system/prometheus.service
```
```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/prometheus/prometheus --config.file=/usr/local/prometheus/prometheus.yml --storage.tsdb.path=/usr/local/prometheus/data

[Install]
WantedBy=multi-user.target
```
### Step 4: Configure Prometheus to Scrape Data from Node Exporter
```bash
sudo nano /usr/local/prometheus/prometheus.yml
```
```
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: "node_exporter"
    static_configs:
      - targets: ["192.168.10.11:9100"]
```
### Step 5: Start Prometheus Service
```bash
~# sudo systemctl daemon-reload
~# sudo systemctl enable prometheus
~# sudo systemctl start prometheus
```
### Step 6: Check the status of the Prometheus service to ensure it is running:
```
sudo systemctl status prometheus
```

## 2. Grafana Installation and Configuration

Let's start by installing Grafana on **Server 1**.

### Step 1: Install Grafana
On Server 1, run the following commands to install Grafana:
```bash
~# sudo apt-get install -y software-properties-common
~# sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
~# wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
~# sudo apt-get update
~# sudo apt-get install grafana -y
```
### Step 2: Reload systemd daemon to pick up new unit files:
```
sudo systemctl daemon-reload
```
### Step 3: Enable the Grafana service to start automatically at boot:
```
sudo systemctl enable grafana-server
```
### Step 4: Start the Grafana service:
```
sudo systemctl start grafana-server
```
### Step 5: Check the status of the Grafana service to ensure it is running:
```
sudo systemctl status grafana-server
```
### Step 6: Access Grafana Web Console:

Once Grafana is installed, you can access the Grafana login page through the following URL: http://<server1_public_ip>:3000

The default login credentials are:

  Username: admin
  
  Password: admin
  
On the first login, you will be prompted to change the password for the admin account

### Step 7: Configure Prometheus as a Data Source in Grafana

1. **Navigate to Data Sources**
   - From the left-hand side menu, click on the gear icon (⚙️) to open **Configuration**.
   - Select **Data Sources** from the menu.
     
2. **Add Prometheus Data Source**
   - In the **Data Sources** section, click on **Add data source**.
   -  Choose **Prometheus** from the list of available data sources.
     
3. **Enter the Prometheus URL**
   - In the **URL** field, enter the following URL:
     ```
     http://localhost:9090
     ```
   - This assumes both Grafana and Prometheus are running on the same server.
   - If Prometheus is running on a separate server, replace `localhost` with the IP address of the Prometheus server in the URL field.
     ```
     http://<Prometheus_Server_IP>:9090
     ```
4. **Save the Configuration**
   - After entering the URL, click **Save & Test** to save the configuration and verify the connection.

Once the data source is configured, you can start creating dashboards using Prometheus data in Grafana.

## 3. Node Exporter Installation and Configuration

Now, let's move on to Server 2.

### Step 1: Install Node Exporter
```bash
~# cd /tmp
~# curl -LO https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
~# tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz
~# sudo mv node_exporter-1.6.1.linux-amd64 /usr/local/node_exporter
```
### Step 2: Create a Dedicated User and Group for node_exporter:
```bash
sudo useradd -rs /bin/false node_exporter
```
### Step 3: Create a Systemd Service for Node Exporter
```bash
sudo nano /etc/systemd/system/node_exporter.service
```
```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/node_exporter/node_exporter

[Install]
WantedBy=multi-user.target

```
### Step 4: Start Node Exporter Service
```bash
~# sudo systemctl daemon-reload
~# sudo systemctl enable node_exporter
~# sudo systemctl start node_exporter
```
### Step 5: Verifications
1. **Verify Node Exporter Metrics**
   - To verify if Node Exporter is running correctly, load the following URL:
   - `http://<server2_ip>:9100/metrics`
     
2. **Verify Prometheus Data Collection**
   - To verify if Prometheus is correctly collecting data from Node Exporter, load the following URL:
   - `http://<server1_ip>:9090`
   -  In the Prometheus web console, navigate to Status > Targets. You should see the Node Exporter instance listed there.
