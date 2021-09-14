# Transferring Tokens On Casper

Step 4 of [Get started with Casper](https://gitcoin.co/issue/casper-network/gitcoin-hackathon/29/100026611) from the Casper Hackathon.

> Learn to transfer tokens to an account on the Casper Testnet.

### Transfer Accounts

In this task we follow [Transfer Workflow](https://docs.casperlabs.io/en/latest/workflow/transfer-workflow.html) section of the Casper Documentation.

This involves using casper-client to transfer CSPR between local test accounts. We will use the faucet account and one of the other accounts on the node.

Use **nctl-view-faucet-account** to get the details for the faucet account. Then the following to get the balance for the main purse:

```
# Get purse uRef
casper-client get-account-info --node-address http://localhost:11101 \
  --public-key 0132263f5134d3d69026ec4575fecca26ba362266acfbb979cfc2c536bafa34a6e

# Get latest state root hash
casper-client get-state-root-hash --node-address http://localhost:11101

# Get balance
casper-client get-balance --node-address http://localhost:11101 \
  --purse-uref uref-261ec7b1b7206934e34611ce798454b17e62b39f5bf01796dd5d64288a75611c-00 \
  --state-root-hash 40022f45343854a6e3956bcd71d8aaf92e77f69346394a3389186c3fd04b9594
```
The balance for the faucet account is **1000000000000000000000000000000000**.

To find other accounts:

```
nctl-view-user-account
# Gives account key: 013284d8980fcc528649edf7e9fb67760dc4533252ad60ec62a7c657b13527a01c
casper-client get-balance --node-address http://localhost:11101 \
  --purse-uref uref-91870dc6bd83afc6f3176f79549a733eb70e58561db87a5614c7952c868cd6b2-007 \
  --state-root-hash 40022f45343854a6e3956bcd71d8aaf92e77f69346394a3389186c3fd04b9594
```

The second account has a balance of **1000000000000000000000000000000000**.

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
    <--payment-amount <AMOUNT>|--payment-path <PATH>|--payment-package-hash <HEX STRING>|
    --payment-package-name <NAME>|--payment-hash <HEX STRING>|--payment-name <NAME>|--show-arg-examples>
```
The command needed to be updated to add the **--payment-amount** parameter and use **casper-net-1** instead of casper test as written in the [Transfer Workflow Document](https://docs.casperlabs.io/en/latest/workflow/transfer-workflow.html)
```
casper-client transfer --amount 250000000000 --chain-name casper-net-1 \
  --node-address http://localhost:11101 \
  --secret-key /media/chrisb/crypto1/local_node/casper-node/utils/nctl/assets/net-1/faucet/secret_key.pem \
  --target-account 013284d8980fcc528649edf7e9fb67760dc4533252ad60ec62a7c657b13527a01c \
  --transfer-id 123456789012345 --payment-amount 10000
```
The deploy hash is used to find details of the transaction:
```
"deploy_hash": "af19e964025d42a86480738dfd4665c5adef0a4fcc1a06916ffef75175d0714c"
```
We then view the second account with:
```
casper-client get-balance --node-address http://localhost:11101 \
  --purse-uref uref-91870dc6bd83afc6f3176f79549a733eb70e58561db87a5614c7952c868cd6b2-007 
  --state-root-hash 46f21a364ad9cdab20e831e9963d0a9ebe788372c541840b451a5f2ac090e946
```
### Transfer Completed
The balance for the second account is now **1000000000000000000000250000000000**, showing that the transfer has been added.

### Querying Transaction State
We use get-deploy with returned deploy_hash to query the state of the transaction:
```
casper-client get-deploy \
  --node-address http://localhost:11101 af19e964025d42a86480738dfd4665c5adef0a4fcc1a06916ffef75175d0714c
```
We use the information returned to gather the important information from the transaction:
```
# "result"."execution_results"[0]."transfers[0]"
transfer-54d78315d0458409f657e855a8c25381eaac03881dce9ac0c3e03907ff809e81

# "result"."execution_results"[0]."block_hash"
528e275ea3fd2410fb2e7ab860739bdec57e26e638e0f6e46e9cb19a7f797776
```
### Querying Block State
We get the block information using:
```
casper-client get-block --node-address http://localhost:11101 --block-identifier 528e275ea3fd2410fb2e7ab860739bdec57e26e638e0f6e46e9cb19a7f797776
```
### Querying Source Account
We have already queried the destination account. But for completeness we query the source account to make sure the transaction has completed on both sides:
```
casper-client query-state --node-address http://localhost:11101 \
  --key 0132263f5134d3d69026ec4575fecca26ba362266acfbb979cfc2c536bafa34a6e 
  --state-root-hash d796f89349c05d1dbb014c7845c86300db14d8743e014b96e3cbc2d7f09bbbd8

casper-client get-balance --node-address http://localhost:11101 \
  --state-root-hash d796f89349c05d1dbb014c7845c86300db14d8743e014b96e3cbc2d7f09bbbd8 
  --purse-uref uref-261ec7b1b7206934e34611ce798454b17e62b39f5bf01796dd5d64288a75611c-00
```

The balance for the faucet account is now **999999999999999999999749999990000** Showing that the funds and fees have been removed from the source account.
