<img width="2000" height="2000" alt="Black Blue Minimalist Floral Logo" src="https://github.com/user-attachments/assets/8e134c21-4983-4660-843d-098e41202beb" />

cat > README.md << 'EOF'
# 🌐 Arc Network Node & Contributor Hub

Run, sync, and contribute to the Arc Testnet with a full node setup guide.

## Initial Setup

```bash
sudo apt update
sudo apt install -y build-essential pkg-config libssl-dev clang curl git

curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env

curl -L https://foundry.paradigm.xyz | bash
source ~/.bashrc
foundryup
```

## Build Node

```bash
cd ~
git clone https://github.com/circlefin/arc-node.git
cd arc-node
git checkout v0.6.0
git submodule update --init --recursive

cargo install --path crates/node --force
cargo install --path crates/malachite-app --force
cargo install --path crates/snapshots --force

echo 'source ~/.cargo/env' >> ~/.bashrc
source ~/.bashrc
```

## Snapshot Initialization

```bash
mkdir -p ~/.arc/execution ~/.arc/consensus
sudo install -d -o \$USER /run/arc
arc-snapshots download --chain=arc-testnet
```

## Terminal 1 → Execution Layer

```bash
arc-node-execution node \
  --chain arc-testnet \
  --datadir ~/.arc/execution \
  --disable-discovery \
  --ipcpath /run/arc/reth.ipc \
  --auth-ipc \
  --auth-ipc.path /run/arc/auth.ipc \
  --http \
  --http.addr 127.0.0.1 \
  --http.port 8545 \
  --http.api eth,net,web3,txpool,trace,debug \
  --metrics 127.0.0.1:9001 \
  --enable-arc-rpc \
  --rpc.forwarder https://rpc.quicknode.testnet.arc.network/
```

<img width="1445" height="734" alt="1" src="https://github.com/user-attachments/assets/998970bb-d33e-488b-85b4-894cd93e450d" />


## Terminal 2 → Consensus Layer

```bash
arc-node-consensus init --home ~/.arc/consensus

arc-node-consensus start \
  --home ~/.arc/consensus \
  --eth-socket /run/arc/reth.ipc \
  --execution-socket /run/arc/auth.ipc \
  --rpc.addr 127.0.0.1:31000 \
  --follow \
  --follow.endpoint https://rpc.drpc.testnet.arc.network,wss=rpc.drpc.testnet.arc.network \
  --follow.endpoint https://rpc.quicknode.testnet.arc.network,wss=rpc.quicknode.testnet.arc.network \
  --follow.endpoint https://rpc.blockdaemon.testnet.arc.network,wss=rpc.blockdaemon.testnet.arc.network \
  --metrics 127.0.0.1:29000
```

<img width="1462" height="767" alt="2" src="https://github.com/user-attachments/assets/eb257fdd-8e41-4f8e-8458-8c9721c1d139" />


## Check Node

```bash
cast block-number --rpc-url http://localhost:8545
```

<img width="660" height="45" alt="3" src="https://github.com/user-attachments/assets/df6b7808-bd7a-47c5-ad0a-b44ef9e444d5" />

If block numbers increase continuously, your node is healthy.
EOF
