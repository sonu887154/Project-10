# Project-10
LABS-STORAGE-NODE-RUN-GUIDE-BY-New

# 😱😱 0G-LABS-STORAGE-NODE-RUN-GUIDE

> 🚀 Complete guide to run your own 0G Labs Storage Node with snapshot support, logs monitoring, and systemd service setup.  


---

## 🖥️ Node Management

### 🔄 Reattach to Node Screen:
```bash
screen -r og
````

### 📋 List All Screens:

```bash
screen -ls
```

### 🔁 Attach to Any Running Screen:

```bash
screen -r
```

## 💻 System Requirements

- Linux VPS (Ubuntu 20.04 or later)
- Minimum hardware: 4-core CPU, 8GB RAM, 200GB SSD


## ⚙️ Pre-Setup

### 🟢 Add 0G RPC:

```
https://chainscan-galileo.0g.ai/
```

### 💧 Get Faucet:

```
https://faucet.0g.ai/
```

## 🔧 Install Dependencies

```
sudo apt-get update && sudo apt-get upgrade -y
```

```
sudo apt install curl iptables build-essential git wget lz4 jq make cmake gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev screen ufw -y
```

### 🦀 Install Rust

```
curl https://sh.rustup.rs -sSf | sh
```

```
source $HOME/.cargo/env
```

Check version

```
rustc --version
```

### 🟡 Install Go

```
wget https://go.dev/dl/go1.24.3.linux-amd64.tar.gz && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf go1.24.3.linux-amd64.tar.gz && \
rm go1.24.3.linux-amd64.tar.gz && \
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc && \
source ~/.bashrc
```

check version

```
go version
```

## 🖥 Create Node Screen

```bash
screen -S og
```

## 🔁 Clone and Build the Storage Node

```
git clone https://github.com/0glabs/0g-storage-node.git
```

```
cd 0g-storage-node && git checkout v1.0.0 && git submodule update --init
```

* Build in release mode 

```
cargo build --release
```

## ⚙️ Configure Your Node

```
rm -rf $HOME/0g-storage-node/run/config.toml
```

```bash
curl -o $HOME/0g-storage-node/run/config.toml https://raw.githubusercontent.com/Naveenrawde3/0G-LABS-STORAGE-NODE-RUN-GUIDE-BY-NTEK/main/config.toml
```


### 🛠 Add Your Private Key      Dont Add **0X** before the key:  

Edit `config.toml`:

```
nano $HOME/0g-storage-node/run/config.toml
```

→ Paste your private key under `miner_key` (without `0x`)

---

## 🌐 Change RPC

* Visit: [https://www.astrostake.xyz/0g-status](https://www.astrostake.xyz/0g-status)
* Pick a fresh RPC and edit `config.toml`

---

## 🛠 Setup as Systemd Service

```
sudo tee /etc/systemd/system/zgs.service > /dev/null <<EOF
[Unit]
Description=ZGS Node
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/0g-storage-node/run
ExecStart=$HOME/0g-storage-node/target/release/zgs_node --config $HOME/0g-storage-node/run/config.toml
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

* Reload

```
sudo systemctl daemon-reload
```

* Enable

```
sudo systemctl enable zgs
```

* Start service

```
sudo systemctl start zgs
```

## 📡 Monitoring and Logs

```bash
sudo systemctl status zgs
```

* check Full Logs

```
tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d)
```

### 🔄 Check Syncing:

```
 while true; do     response=$(curl -s -X POST http://localhost:5678 -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"zgs_getStatus","params":[],"id":1}');     logSyncHeight=$(echo $response | jq '.result.logSyncHeight');     connectedPeers=$(echo $response | jq '.result.connectedPeers');     echo -e "logSyncHeight: \033[32m$logSyncHeight\033[0m, connectedPeers: \033[34m$connectedPeers\033[0m";     sleep 5; done
```

## ⚡ Fast Sync with Flow DB Snapshot

🛠️ Installation Instructions

* Stop The Node & Delete flow db

```
sudo systemctl stop zgs
```

```
rm -rf $HOME/0g-storage-node/run/db/flow_db
```

* Download and extract the Flow db:

```bash
wget https://github.com/Naveenrawde3/0G-LABS-STORAGE-NODE-RUN-GUIDE-BY-NTEK-New-/releases/download/v1.0/flow_db.tar.gz \
  -O $HOME/0g-storage-node/run/db/flow_db.tar.gz && \
  tar -xzvf $HOME/0g-storage-node/run/db/flow_db.tar.gz -C $HOME/0g-storage-node/run/db/
```

## 🔁 Restart ZGS Service

```
sudo systemctl restart zgs
```

## 💽 Check Disk Space

```bash
df -h
```

## 🧹 Remove the Node

* If Your Vps storage got full, then u can follow these commands and instruction to Clear it & Do Again:
  
```
sudo systemctl stop zgs
```

```
sudo systemctl disable zgs
sudo rm /etc/systemd/system/zgs.service
rm -rf $HOME/0g-storage-node
```

## Process for Local Device & how to restart on next day

* So, For local PC All the process is same as VPS: You have to start from [Pre-Requirements 🛠]

* 👉 Next Day process:
                  
- Just Open your wsl/terminal and run

```
sudo systemctl restart zgs
```
