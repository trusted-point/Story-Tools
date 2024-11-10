[<img src='assets\Story-Banner.png' alt='banner' width= '99.9%'>]()

<font size = 7><center><b><u>About Story</u></b></center></font>

* ### **[Story](https://www.story.foundation)** is making the legal system for creative Intellectual Property (IP) more efficient by turning IP "programmable" on the blockchain. To do this, we have created Story Network: a purpose-built layer 1 blockchain where people or programs alike can license, remix, and monetize IP according to transparent terms set by creators themselves.

<div align="center">
  <a href="https://www.story.foundation"><img src="https://github.com/bartosian/celestia-tools/assets/20209819/85aaef48-7ea4-4857-b339-985645c6850c" alt="Website" width="16%"></a>
  <a href="https://github.com/storyprotocol"><img src="https://github.com/bartosian/celestia-tools/assets/20209819/229ec400-72ff-48ee-ac18-7bdb1f5e221a" alt="Github" width="16%"></a>
  <a href="https://x.com/StoryProtocol"><img src="https://github.com/bartosian/celestia-tools/assets/20209819/3978b7fc-d575-44a6-8d41-327c14c8ba31" alt="Twitter" width="16%"></a>
  <a href="https://discord.com/invite/storyprotocol"><img src="https://github.com/bartosian/celestia-tools/assets/20209819/944a0b87-548d-4109-ad0c-def572d307cb" alt="Discord" width="16%"></a>
  <a href="https://medium.com/@storyprotocol"><img src="https://github.com/bartosian/celestia-tools/assets/20209819/ac52729b-64d7-44d1-9a66-1e0d159848f6" alt="Blog" width="16.2%"></a>
</div>

- [TrustedPoint Services](#trustedpoint-services)
- [Installation guide](#installation-guide)
  - [1. Install required packages](#1-install-required-packages)
  - [2. Install Go](#2-install-go)
  - [3. Build `story` binary](#3-build-story-binary)
  - [3. Build `geth` binary](#3-build-geth-binary)
  - [4. initialize story client working env](#4-initialize-story-client-working-env)
  - [5. Create story client service file](#5-create-story-client-service-file)
  - [6. Start story client node](#6-start-story-client-node)
  - [7. Create geth service file](#7-create-geth-service-file)
  - [8. Start geth node](#8-start-geth-node)
- [Download Snapshots](#download-snapshots)
  - [1. Check snapshot info](#1-check-snapshot-info)
  - [2. Download story client snapshot](#2-download-story-client-snapshot)
  - [3. Download geth snapshot](#3-download-geth-snapshot)
  - [4. Stop the nodes](#4-stop-the-nodes)
  - [5. Backup priv\_validator\_state.json](#5-backup-priv_validator_statejson)
  - [6. Reset DBs](#6-reset-dbs)
  - [7. Extract story client snapshot](#7-extract-story-client-snapshot)
  - [8. Extract geth snapshot](#8-extract-geth-snapshot)
  - [9. Move priv\_validator\_state.json back](#9-move-priv_validator_statejson-back)
  - [10. Restart the nodes](#10-restart-the-nodes)
  - [8. Check the synchronization status](#8-check-the-synchronization-status)
```py
- Memory: 16 GB RAM
- CPU: 4 Cores
- Disk: 200 GB NVME
- Bandwidth: 25 MBit/s
```
## TrustedPoint Services
---
- COSMOS RPC: https://rpc-story-testnet.trusted-point.com:443
- COSMOS REST API (SWAGGER): https://api-story-testnet.trusted-point.com:443
- COSMOS WSS: wss://rpc-story-testnet.trusted-point.com:443/websocket
- EVM RPC: https://evm-rpc-story-testnet.trusted-point.com:443
- EVM WSS: wss://evm-rpc-story-testnet.trusted-point.com:443/websocket

## Installation guide

### 1. Install required packages
```bash
sudo apt update && \
sudo apt install curl git jq build-essential gcc unzip wget lz4 -y
```
### 2. Install Go
```bash
cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
### 3. Build `story` binary
```bash
git clone https://github.com/piplabs/story
cd ./story
git checkout v0.12.1
go build -o story ./client
cp ./story $HOME/go/bin
story version
```
### 3. Build `geth` binary
```bash
git clone https://github.com/piplabs/story-geth.git
cd ./story-geth
git checkout v0.10.0
make geth
cp ./build/bin/geth $HOME/go/bin
geth version
```
### 4. initialize story client working env
```bash
story init --network odyssey --moniker "Your_Node_Name"
```
### 5. Create story client service file
```bash
sudo tee /etc/systemd/system/story.service > /dev/null <<EOF
[Unit]
Description=Story Client
After=network.target

[Service]
User=$USER
WorkingDirectory=$USER/.story/story
Type=simple
ExecStart=$(which story) run
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
### 6. Start story client node
```bash
sudo systemctl daemon-reload && \
sudo systemctl enable story && \
sudo systemctl restart story && \
sudo journalctl -u story -f -o cat
```
### 7. Create geth service file
```bash
sudo tee /etc/systemd/system/geth.service > /dev/null <<EOF
[Unit]
Description=Geth Client
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which geth) --odyssey --syncmode full
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
### 8. Start geth node
```bash
sudo systemctl daemon-reload && \
sudo systemctl enable geth && \
sudo systemctl restart geth && \
sudo journalctl -u geth -f -o cat
```

P.S. Consider [downloading snapshot](#download-snapshot)

## Download Snapshots

### 1. Check snapshot info
```bash
curl -s https://storage.trusted-point.com/snapshots/story/snapshot_info.json
```
### 2. Download story client snapshot
```bash
wget https://storage.trusted-point.com/snapshots/story/latest_story_snapshot.tar.lz4
```
### 3. Download geth snapshot
```bash
wget https://storage.trusted-point.com/snapshots/story/latest_geth_snapshot.tar.lz4
```
### 4. Stop the nodes
```bash
sudo systemctl stop story
sudo systemctl stop geth
```
### 5. Backup priv_validator_state.json 
```bash
cp $HOME/.story/story/data/priv_validator_state.json $HOME/.story/story/priv_validator_state.json.backup
```
### 6. Reset DBs
```bash
rm -rf $HOME/.story/story/data
rm -rf $HOME/.story/geth/odyssey/geth/chaindata
```
### 7. Extract story client snapshot
```bash
lz4 -d -c ./latest_story_snapshot.tar.lz4 | tar -xf - -C $HOME/.story/story
```
### 8. Extract geth snapshot
```bash
lz4 -d -c ./latest_geth_snapshot.tar.lz4 | tar -xf - -C $HOME/.story/geth/odyssey/geth
```
### 9. Move priv_validator_state.json back
```bash
mv $HOME/.story/story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json
```
### 10. Restart the nodes
```bash
sudo systemctl restart story && sudo journalctl -u story -f -o cat
sudo systemctl restart geth && sudo journalctl -u geth -f -o cat
```
### 8. Check the synchronization status
```bash
wardend status | jq .SyncInfo
```
Snapshot is being updated every 6 hours
