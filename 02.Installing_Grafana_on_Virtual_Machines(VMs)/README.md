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
