# Counter Contract On Casper

Step 2 of [Get started with Casper](https://gitcoin.co/issue/casper-network/gitcoin-hackathon/29/100026611) from the Casper Hackathon.

> Complete one of the existing tutorials for writing smart contracts

We document followoing the documentation for creating a [Counter Contract](https://docs.casperlabs.io/en/latest/dapp-dev-guide/tutorials/counter).

### 1. Introduction

For local development we will use a faucet account to deploy contracts.

> This command supplies two key pieces of information: the account’s secret key location and the account hash, which are used to sign deploys and query the network state, respectively.

**casper-client** interacts with the network. a *State Root Hash* shows the current state of the network and helps us check if updates have been made. **casper-client query-state** allows the state of network to be viewed at any time. **casper-client put-deploy** deploys wasm contracts to the network. **casper-client get-deploy** gets the details for a deployed contract.
