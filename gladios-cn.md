## Gladios 预言机网络

## 搭建验证人节点

### 安装代码编译工具

* 安装 Rust
```shell
# Install
curl https://sh.rustup.rs -sSf | sh
# Configure
source ~/.cargo/env
```

* 配置 Rust 工具链默认为最新的稳定版本，添加 nightly 和 nightly wasm 编译目标。
```shell
rustup default stable
rustup update
rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly
```

* 安装依赖项 (举例 Ubuntu 18.04) 其他操作系统的安装，可以参考 [Substrate开发者文档](https://substrate.dev/docs/en/knowledgebase/getting-started/#1-build-dependencies)
```shell
sudo apt update
sudo apt install make clang pkg-config libssl-dev build-essential
```

### 获取节点可执行文件

* 获取 Gladios 项目源码并编译。
```shell
git clone git@github.com:aresprotocols/ares.git ares-chain
cd ares-chain
cargo build --release
```
* 编译后会获得可执行文件 ares-chain/target/release/gladios-node 

### 启动数据节点

* 启动数据节点并连接到网络。
```shell
# 仍然在刚才编译的目录中执行，如下命令。
./target/release/gladios-node \
  --base-path /tmp/gladios-data \
  --name ARES_DATA_NODE \
  --chain ./chain-data-ares-aura.json \
  --port 30334 \
  --ws-port 9946 \ 
  --rpc-port 9934 \
  --ws-external \
  --rpc-external \
  --rpc-cors=all \
  --rpc-methods=Unsafe \
  --telemetry-url 'wss://telemetry.polkadot.io/submit/ 0' \
  --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWEyoppNCUx8Yx66oV9fJnriXwCcXwDDUA2kj6vnc6iDEp
```

### 启动验证人节点

* 启动验证人节点并连接到网络。（Aura共识）
```shell
# 仍然在刚才编译的目录中执行，如下命令。
./target/release/gladios-node purge-chain --base-path /tmp/aura/two --chain gladios -y
./target/release/gladios-node \
  --base-path /tmp/gladios-data \
  --name ARES_VALIDATOR_NODE \
  --chain ./chain-data-ares-aura.json \
  --port 30335 \
  --ws-port 9947 \
  --rpc-port 9935 \
  --ws-external \
  --rpc-external \
  --rpc-cors=all \
  --rpc-methods=Unsafe \
  --telemetry-url 'wss://telemetry.polkadot.io/submit/ 0' \
  --warehouse http://141.164.58.241:5566 \
  --ares-keys ./your_ares_key_file.curl \
  --validator \
  --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWEyoppNCUx8Yx66oV9fJnriXwCcXwDDUA2kj6vnc6iDEp
```
* --warehouse 参数，用来指定对应验证人的报价服务器请求地址及端口。
* --ares-keys 参数，用来指定 ares 以及其他可能需要启动的私钥信息，比如 aura 或者 gran。
```shell
# ares-keys 指定的文件举例，注意该文件不能包括注释，按换行符进行分割，多数情况下 ares 钥匙对应与出块人保持一致。
ares:finger treat seven sign army beauty album zebra fiction office planet tragic
aura:finger treat seven sign army beauty album zebra fiction office planet tragic
gran:ensure usage check coast suspect warrior extend young frequent track can cloud
```

### 节点查看
* 访问：https://telemetry.polkadot.io/#list/0x91b171bb158e2d3848fa23a9f1c25182fb8e20313b2c1eb49219da7a70ce90c3 