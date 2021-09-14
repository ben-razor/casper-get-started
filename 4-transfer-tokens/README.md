# Transferring Tokens On Casper

Step 4 of [Get started with Casper](https://gitcoin.co/issue/casper-network/gitcoin-hackathon/29/100026611) from the Casper Hackathon.

> Learn to transfer tokens to an account on the Casper Testnet.

### Transfer Accounts

In this task we follow [Transfer Workflow](https://docs.casperlabs.io/en/latest/workflow/transfer-workflow.html) section of the Casper Documentation.

This involves using casper-client to transfer CSPR between local test accounts. We will use the faucet account and one of the other accounts on the node.

Use **nctl-view-faucet-account** to get the details for the faucet account. Then the following to get the balance for the main purse:

```
# Get purse uRef
casper-client get-account-info --node-address http://localhost:11101 --public-key 0132263f5134d3d69026ec4575fecca26ba362266acfbb979cfc2c536bafa34a6e

# Get latest state root hash
casper-client get-state-root-hash --node-address http://localhost:11101

# Get balance
casper-client get-balance --node-address http://localhost:11101 --purse-uref uref-261ec7b1b7206934e34611ce798454b17e62b39f5bf01796dd5d64288a75611c-00 --state-root-hash 40022f45343854a6e3956bcd71d8aaf92e77f69346394a3389186c3fd04b9594
```
The balance for the faucet account is 1000000000000000000000000000000000.

To find other accounts:

```
nctl-view-user-account
# Gives account key: 013284d8980fcc528649edf7e9fb67760dc4533252ad60ec62a7c657b13527a01c
casper-client get-balance --node-address http://localhost:11101 --purse-uref uref-91870dc6bd83afc6f3176f79549a733eb70e58561db87a5614c7952c868cd6b2-007 --state-root-hash 40022f45343854a6e3956bcd71d8aaf92e77f69346394a3389186c3fd04b9594
```

This account has a balance of 1000000000000000000000000000000000.

### Making The Transfer

With this information in hand we use the following command to make the transfer:
```
casper-client transfer \
    --id 1 \
    --transfer-id 123456789012345 \
    --node-address http://localhost:11101 \
    --amount 1000000000 \
    --secret-key /media/chrisb/crypto1/local_node/casper-node/utils/nctl/assets/net-1/faucet/secret_key.pem \
    --chain-name casper-test \
    --target-account 013284d8980fcc528649edf7e9fb67760dc4533252ad60ec62a7c657b13527a01c
```
**The command fails with the error:**
```
error: The following required arguments were not provided:
    <--payment-amount <AMOUNT>|--payment-path <PATH>|--payment-package-hash <HEX STRING>|--payment-package-name <NAME>|--payment-hash <HEX STRING>|--payment-name <NAME>|--show-arg-examples>
```

The command needed to be updated to add the --payment-amount parameter and use casper-net-1 instead of casper test as written in the [Transfer Workflow Document](https://docs.casperlabs.io/en/latest/workflow/transfer-workflow.html)
```
casper-client transfer --amount 250000000000 --chain-name casper-net-1 --node-address http://localhost:11101 --secret-key /media/chrisb/crypto1/local_node/casper-node/utils/nctl/assets/net-1/faucet/secret_key.pem --target-account 013284d8980fcc528649edf7e9fb67760dc4533252ad60ec62a7c657b13527a01c --transfer-id 123456789012345 --payment-amount 10000
```
We then view the second account with:
```
casper-client get-balance --node-address http://localhost:11101 --purse-uref uref-91870dc6bd83afc6f3176f79549a733eb70e58561db87a5614c7952c868cd6b2-007 --state-root-hash 46f21a364ad9cdab20e831e9963d0a9ebe788372c541840b451a5f2ac090e946
```
The balance for the second account is now:
```
"balance_value": "1000000000000000000000250000000000"
```
