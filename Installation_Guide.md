# OpenSIEM Installation Guide

This guide provides step-by-step instructions for setting up the complete OpenSIEM environment.

## Prerequisites

First, install the common dependencies required by all components:

```bash
sudo apt update
sudo apt install wget gnupg apt-transport-https git ca-certificates ca-certificates-java curl software-properties-common python3-pip lsb-release
```

### 1. Installing TheHive 5

Java Installation

```bash
wget -qO- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor -o /usr/share/keyrings/corretto.gpg
echo "deb [signed-by=/usr/share/keyrings/corretto.gpg] https://apt.corretto.aws stable main" | sudo tee -a /etc/apt/sources.list.d/corretto.sources.list
sudo apt update
sudo apt install java-common java-11-amazon-corretto-jdk
echo JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto" | sudo tee -a /etc/environment
export JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"
```

### Cassandra Installation

```bash
wget -qO - https://downloads.apache.org/cassandra/KEYS | sudo gpg --dearmor -o /usr/share/keyrings/cassandra-archive.gpg
echo "deb [signed-by=/usr/share/keyrings/cassandra-archive.gpg] https://debian.cassandra.apache.org 40x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
sudo apt update
sudo apt install cassandra
```

### Elasticsearch Installation

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
sudo apt-get install apt-transport-https
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install elasticsearch
```

### 2. Installing Wazuh 4.7

```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```

### 3. Installing Shuffle

Docker Installation

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
```

### 4. Integration

Wazuh-TheHive Integration

Update Wazuh configuration:
```bash
sudo tee -a /var/ossec/etc/ossec.conf > /dev/null << 'EOF'

custom-thehive
<hook_url>http://localhost:9000</hook_url>
7
authentication_failure,authentication_success
<alert_format>json</alert_format>

EOF
sudo systemctl restart wazuh-manager
```

### 5. Post-Installation

Enable services on boot:
```bash
sudo systemctl enable elasticsearch cassandra thehive wazuh-manager wazuh-indexer wazuh-dashboard
```
