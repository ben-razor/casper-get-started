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

As part of its operation, the Keys Manager deploys the contract that was created earlier with:
```JavaScript
   console.log("\n[x] Install Keys Manager contract");
    let deploy = keyManager.keys.buildContractInstallDeploy(mainAccount);
    await keyManager.sendDeploy(deploy, [mainAccount]);
```

**The combined weights of the keys must beat the action_threshold in order for specific actions to be carried out on an account**
```JavaScript
deploy = utils.transferDeploy(mainAccount, firstAccount, 1);
// Works as the deployment threshold is 1 and the keys combined weight is 2
await utils.sendDeploy(deploy, [firstAccount, secondAccount]); 
```

### Additional Scenarios

There are a number of [different scenarios](https://docs.casperlabs.io/en/latest/dapp-dev-guide/tutorials/multi-sig/examples.html) that 
can be implemented when using multiple keys.

To extend this example we will implement **Scenario 4: managing lost or stolen keys**.

To implement this example we made the following changes to scenario-atomic.js:

```JavaScript
const keyManager = require('./key-manager');
const TRANSFER_AMOUNT = process.env.TRANSFER_AMOUNT || 2500000000;

(async function () {

    // In this example, we set up 3 keys. A "browser" key (main), "mobile" key,
    // and "safe" key
    //
    // Safe can do deployment and management on its own.
    // Browser + Mobile can be used to deploy, but can't manage accounts without Safe
    
    // To achive the task, we will:
    // 1. Set Keys Management Threshold to 3.
    // 2. Set Deploy Threshold to 2.
    // 3. Set main (browser), and mobile weight to 1
    // 4. Set Safe key weight to 3 
    // 4. Make a transfer from mainAccount to secondAccount using first & second accounts.
    // 
    // 1, 2, 3 will be done in one step.

    const masterKey = keyManager.randomMasterKey();
    const mainAccount = masterKey.deriveIndex(1);
    const firstAccount = masterKey.deriveIndex(2);
    const secondAccount = masterKey.deriveIndex(3);

    console.log("Main account: " + mainAccount.publicKey.toHex());
    console.log("First account: " + firstAccount.publicKey.toHex());
    console.log("Second account: " + secondAccount.publicKey.toHex());

    console.log("\n[x] Funding main account.");
    await keyManager.fundAccount(mainAccount);
    await keyManager.printAccount(mainAccount);

    console.log("\n[x] Install Keys Manager contract");
    let deploy = keyManager.keys.buildContractInstallDeploy(mainAccount);
    await keyManager.sendDeploy(deploy, [mainAccount]);
    await keyManager.printAccount(mainAccount);

    // Deploy threshold is 2 out of 4
    const deployThereshold = 2;
    // Key Managment threshold is 3 out of 4
    const keyManagementThreshold = 3;
    // Every account weight is set to 1
    const accounts = [
        { publicKey: mainAccount.publicKey, weight: 1 },
        { publicKey: firstAccount.publicKey, weight: 1 }, 
        { publicKey: secondAccount.publicKey, weight: 3 }, 
    ];

    console.log("\n[x] Update keys deploy.");
    deploy = keyManager.keys.setAll(mainAccount, deployThereshold, keyManagementThreshold, accounts);
    await keyManager.sendDeploy(deploy, [mainAccount]);
    await keyManager.printAccount(mainAccount);

    console.log("\n[x] Make transfer.");
    deploy = keyManager.transferDeploy(mainAccount, secondAccount, TRANSFER_AMOUNT);
    await keyManager.sendDeploy(deploy, [firstAccount, secondAccount]);
    await keyManager.printAccount(mainAccount);
})();
```
Adding the following to package.json:
```
"scripts": {
    ...
    "start:lost-stolen": "node -r dotenv/config ./src/scenario-lost-stolen.js",
    ...
}
```

The following output was produced by the script, confirming that lost or stolen key configuration was set up:

```
{
  _accountHash: 'account-hash-c4f07b3a5489486df5db9d4aa29416c56b1e864bbe69b6c57c6f2c3d60ef7784',
  namedKeys: [
    {
      name: 'keys_manager',
      key: 'hash-cfb307cd5be2c3034582117740b1bdd0feea0970479ffb9f6cdd39322f22ff0f'
    },
    {
      name: 'keys_manager_hash',
      key: 'uref-a92ab850d14ce90ff7eb0ff43b0d5f958e67f5c461367384a54a204039e252f5-007'
    }
  ],
  mainPurse: 'uref-5b4790b244d066087a42c57c8ed033d4fea59e05a5676d5561c7efa5d8fdc0a4-007',
  associatedKeys: [
    {
      accountHash: 'account-hash-14f851d3d58432c04cc0ea439a41c34ddd6cf7606470f69286216ac06503f3e0',
      weight: 3
    },
    {
      accountHash: 'account-hash-73d68303dfc3d43a8c9ec04704ff85b2ddc167b62a6d073bc0b2a8787ef4b1bc',
      weight: 1
    },
    {
      accountHash: 'account-hash-c4f07b3a5489486df5db9d4aa29416c56b1e864bbe69b6c57c6f2c3d60ef7784',
      weight: 1
    }
  ],
  actionThresholds: { deployment: 2, keyManagement: 3 }
}
```
### Task Screenshots
A selection of screenshots taken during this task:
#### Output from tutorial script
![task-3-atomic-output.png](https://github.com/ben-razor/casper-get-started/blob/main/3-multi-signature/img-task-3/task-3-atomic-output.png)
#### Output from Additional Scenario
![task-3-output-1.png](https://github.com/ben-razor/casper-get-started/blob/main/3-multi-signature/img-task-3/task-3-output-1.png)
