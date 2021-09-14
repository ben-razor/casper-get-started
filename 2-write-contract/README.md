# Counter Contract On Casper

Step 2 of [Get started with Casper](https://gitcoin.co/issue/casper-network/gitcoin-hackathon/29/100026611) from the Casper Hackathon.

> Complete one of the existing tutorials for writing smart contracts

We document following the tutorial for creating a [Counter Contract](https://docs.casperlabs.io/en/latest/dapp-dev-guide/tutorials/counter).

### 1. Introduction

For local development we will use a faucet account to deploy contracts.

> This command supplies two key pieces of information: the account’s secret key location and the account hash, which are used to sign deploys and query the network state, respectively.

**casper-client** interacts with the network. a *State Root Hash* shows the current state of the network and helps us check if updates have been made. **casper-client query-state** allows the state of network to be viewed at any time. **casper-client put-deploy** deploys wasm contracts to the network. **casper-client get-deploy** gets the details for a deployed contract.

### 2. Casper Network Client Setup

First we follow a separate tutorial to install a local Casper Node and the [network control tool](https://docs.casperlabs.io/en/latest/dapp-dev-guide/setup-nctl.html). This installs a Python virtual environment and some required Python modules along with some system libraries.

From within the venv, we download and install the Casper node tools:

```
git clone https://github.com/casper-network/casper-node-launcher
git clone https://github.com/casper-network/casper-node
source casper-node/utils/nctl/activate
nctl-compile
```

### 3. Start Local Network

```bash
nctl-assets-setup && nctl-start
```

### 4. Install casper-client

Using the commands from [Casper Client Setup](https://docs.casperlabs.io/en/latest/dapp-dev-guide/tutorials/counter/setup.html)

```
rustup toolchain install nightly
cargo +nightly-2021-06-17 install casper-client
```

Produced the error:

```
Installing casper-client v1.3.2
...
 failed to select a version for `snow`.
      ... required by package `libp2p-noise v0.29.0`
      ... which is depended on by `libp2p v0.35.1`
      ... which is depended on by `casper-node v1.3.2`
      ... which is depended on by `casper-client v1.3.2`
  versions that meet the requirements `^0.7.1` are: 0.7.2, 0.7.1

```

The following commands fixed the problem:

```
rustup toolchain install nightly-2021-06-17
cargo +nightly-2021-06-17 install casper-client --locked
```

Now we can use casper-client to interact with the local node.

### Account / Node Configuration

An account is needed to interact with the contracts. Details for a local test account are produced by **nctl-view-faucet-account**

```
Account hash: account-hash-18095494c7d5169d4ee8dbd72da1dad1a5d296587ae20fe7848a9032ae7d43d0
Secret key: casper-node/utils/nctl/assets/net-1/faucet/secret_key.pem
```

We also need a *State Root Hash* for the server using **casper-client get-state-root-hash --node-address http://localhost:11101**

```
State root hash: cbfa15f64ca8689978c8b6b9cc05cc990b68cd62e3c8d1eb961effc8d8e420eb
```

These are used to query the state of the local node:

```
casper-client query-state --node-address http://localhost:11101 \
    --state-root-hash cbfa15f64ca8689978c8b6b9cc05cc990b68cd62e3c8d1eb961effc8d8e420eb \
    --key account-hash-18095494c7d5169d4ee8dbd72da1dad1a5d296587ae20fe7848a9032ae7d43d0
```

### Deploying Contracts

First compile to wasm using:

```
make prepare
make test
```
Then deploy with:
```
casper-client put-deploy \
    --node-address http://localhost:11101 \
    --chain-name casper-net-1 \
    --secret-key /media/chrisb/crypto1/local_node/casper-node/utils/nctl/assets/net-1/faucet/secret_key.pem \
    --payment-amount 5000000000000 \
    --session-path ./counter/target/wasm32-unknown-unknown/release/counter-define.wasm
```
This produced a depoloy hash that was used to get information about the deployed contract:
```
casper-client get-deploy --node-address http://localhost:11101 8cee321f32d307ca34bd576822c6e064b12dab0c6dab0eeb68c28ce9296d3ce9
```
