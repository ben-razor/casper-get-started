# Multi Signature On Casper

Step 3 of [Get started with Casper](https://gitcoin.co/issue/casper-network/gitcoin-hackathon/29/100026611) from the Casper Hackathon.

> Demonstrate key management concepts by modifying the client in the Multi-Sig tutorial to address one of the additional scenarios

### Keys Manager Contract

To find out more about keys we work through the [Keys Manager Tutorial](https://docs.casperlabs.io/en/latest/dapp-dev-guide/tutorials/multi-sig/contract.html).

```
git clone https://github.com/casper-ecosystem/keys-manager
cd keys-manager
```
The keys manager allows multiple keys to be added to an account and weights to be specified for different keys.

**The documentation refers to api.rs and lib.rs but neither of these files are in the source, I think their functionality is in main.rs**

We follow the same process as previous examples to build the contract. Enabling the wasm target for rust and running **cargo build --release**.

### Keys Manager Client

The keys manager client is a nodejs client to communicate with the contract.

Configuration is stored in a .env file:

```
BASE_KEY_PATH=/media/chrisb/crypto1/local_node/casper-node/utils/nctl/assets/net-1/faucet/
NODE_URL=http://localhost:11101/rpc
```

Additional configuration options are available: WASM_PATH, NETWORK_NAME, FUND_AMOUNT, PAYMENT_AMOUNT, TRANSFER_AMOUNT. These are set to default values in this example.

Finally, we start the application with:
```
npm install
npm run start:atomic
```
