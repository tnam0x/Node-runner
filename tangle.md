# Tangle network - Guide

## Repair

```sh
wget https://github.com/webb-tools/tangle/releases/download/v0.6.1/tangle-testnet-linux-amd64

sudo apt update && sudo apt upgrade -y
sudo apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev libgmp3-dev tar clang bsdmainutils ncdu unzip llvm libudev-dev make protobuf-compiler -y

curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# choose option 1
source $HOME/.cargo/env
rustup default nightly
rustup update
rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly

git clone https://github.com/webb-tools/tangle.git

```

Cài đặt Wallet của polkadot ở đây [Polkadot-js extension, manage accounts for substrate based chains](https://polkadot.js.org/extension/)

Sau khi install polkadot wallet, mình có thể tạo Wallet và lấy được <passpharse> sử dụng cho việc tạo Key bên dưới

## Generate key

```sh
# it will ask for your suri, enter it.
./tangle-testnet-linux-amd64 key insert --base-path /data/validator/nebula \
--chain ./tangle/chainspecs/testnet/tangle-testnet.json \
--scheme Sr25519 \
--suri <passphrase> \
--key-type acco
```

```sh
# it will ask for your suri, enter it.
./tangle-testnet-linux-amd64 key insert --base-path /data/validator/nebula \
--chain ./tangle/chainspecs/testnet/tangle-testnet.json \
--scheme Sr25519 \
--suri <passphrase> \
--key-type babe
```

```sh
# it will ask for your suri, enter it.
./tangle-testnet-linux-amd64 key insert --base-path /data/validator/nebula \
--chain ./tangle/chainspecs/testnet/tangle-testnet.json \
--scheme Sr25519 \
--suri <passphrase> \
--key-type imon
```

```sh
# it will ask for your suri, enter it.
./tangle-testnet-linux-amd64 key insert --base-path /data/validator/nebula \
--chain ./tangle/chainspecs/testnet/tangle-testnet.json \
--scheme Ecdsa \
--suri <passphrase> \
--key-type role
```

```sh
# it will ask for your suri, enter it.
./tangle-testnet-linux-amd64 key insert --base-path /data/validator/nebula \
--chain ./tangle/chainspecs/testnet/tangle-testnet.json \
--scheme Ed25519 \
--suri <passphrase> \
--key-type gran
```

## Create validator service

```sh
sudo tee /etc/systemd/system/validator.service > /dev/null << EOF
[Unit]
Description=Tangle Validator Node
After=network-online.target
StartLimitIntervalSec=0
 
[Service]
User=root
Restart=always
RestartSec=3
ExecStart=/usr/bin/tangle-testnet-linux-amd64 \
  --base-path /data/validator/nebula8 \
  --name <NODE_NAME>\
  --chain tangle-testnet \
  --node-key-file "/home/<USER>/node-key" \
	--rpc-port 9953 \
  --prometheus-port 9624 \
  --port 30342 \
  --validator \
  --no-mdns \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" 
 
[Install]
WantedBy=multi-user.target
EOF
```

```sh
sudo systemctl daemon-reload
sudo systemctl enable validator
sudo systemctl start validator
sudo journalctl -u validator.service -f
```

## Get session key

```sh
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": 
"author_rotateKeys", "params":[]}' http://localhost:9953
```

Sau khi đã chạy các bước trên, vui lòng truy cập vào link bên dưới để thực hiện các bước kích hoạt validating cho validator của mình

[Polkadot/Substrate Portal](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet-rpc.tangle.tools#/explorer)

Thực hiện các bước theo document của team:

[Start Validating on Tangle – Tangle Network](https://docs.tangle.tools/docs/node/validator/requirements/)
