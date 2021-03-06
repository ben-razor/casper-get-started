# Counter Contract On Casper

Step 2 of [Get started with Casper](https://gitcoin.co/issue/casper-network/gitcoin-hackathon/29/100026611) from the Casper Hackathon.

> Complete one of the existing tutorials for writing smart contracts

We document following the tutorial for creating a [Counter Contract](https://docs.casperlabs.io/en/latest/dapp-dev-guide/tutorials/counter).

Screenshots from during the task are included at the bottom of this document.

### 1. Introduction

For local development we will use a faucet test account to deploy contracts.

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

An account is needed to interact with the contracts. Details for a local test account are produced by running the following commands in the root of the local node:

```
source env/bin/activate
source casper-node/utils/nctl/activate
nctl-view-faucet-account
```
These commands produced the following test account information:
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
This produced a deploy hash that was used to get information about the deployed contract:
```
casper-client get-deploy \
  --node-address http://localhost:11101 8cee321f32d307ca34bd576822c6e064b12dab0c6dab0eeb68c28ce9296d3ce9
```

### Query Contract Abilities

**Remember to get a new State Root Hash after each update**
```
casper-client get-state-root-hash --node-address http://localhost:11101
```
Then a query parameter can be used to return detailed information about each contract:
```
casper-client query-state --node-address http://localhost:11101 \
--state-root-hash db571073ea3c42f311b95b85b010025d9d8368de0527ab7d3d061cb8d436dbd8 \
--key account-hash-18095494c7d5169d4ee8dbd72da1dad1a5d296587ae20fe7848a9032ae7d43d0 -q "counter"
```
Or get even more specific with:
```
casper-client query-state --node-address http://localhost:11101 \
--state-root-hash db571073ea3c42f311b95b85b010025d9d8368de0527ab7d3d061cb8d436dbd8 \
--key account-hash-18095494c7d5169d4ee8dbd72da1dad1a5d296587ae20fe7848a9032ae7d43d0 -q "counter/count"
```

### Interact With Contract
Use **put-deploy** with **session-name** and **session-entry-point** to interact with the contract.
```
casper-client put-deploy \
    --node-address http://localhost:11101 \
    --chain-name casper-net-1 \
    --secret-key /media/chrisb/crypto1/local_node/casper-node/utils/nctl/assets/net-1/faucet/secret_key.pem \
    --payment-amount 5000000000000 \
    --session-name "counter" \
    --session-entry-point "counter_inc"
```
Then after getting a new **state-root-hash**, rerun **query-state**. The CLValue has now changed.

### The counter-call Contract

```
casper-client put-deploy --node-address http://localhost:11101   \
  --chain-name casper-net-1  \
  --secret-key /media/chrisb/crypto1/local_node/casper-node/utils/nctl/assets/net-1/faucet/secret_key.pem \
  --payment-amount 5000000000000 --session-path ./counter/target/wasm32-unknown-unknown/release/counter-call.wasm
```

Gave the deploy_hash:

```
270cf989db8884ff1012635c55c879f9311edb46a612b8f91ea5baebb4603d17
```
After getting a new **state-root-hash** and rerunning **query-state**. The CLValue has incremented by the deployment of this new contract.

### Task Screenshots

#### nctl Start
![Start Nodes](https://github.com/ben-razor/casper-get-started/blob/main/2-write-contract/img-task-2/4-nctl-start.png)

#### Counter Tests Passing
![Counter Tests Passing](https://github.com/ben-razor/casper-get-started/blob/main/2-write-contract/img-task-2/6-counter-tests-passing.png)

#### Deploy Counter Contract
![Deploy Counter Contract](https://github.com/ben-razor/casper-get-started/blob/main/2-write-contract/img-task-2/7-contract-deploy.png)

#### Before Counter Increments
![Before Counter Increment](https://github.com/ben-razor/casper-get-started/blob/main/2-write-contract/img-task-2/8-before-counter.png)

#### After Counter Increments
![After Counter Increment](https://github.com/ben-razor/casper-get-started/blob/main/2-write-contract/img-task-2/9-after-counter.png)
