# Deploying a contract to Evernode

You can use [Evernode developer kit](../evdevkit/index.md) to deploy your [HotPocket smart contracts](../hotpocket/concepts.md#smart-contract) on Evernode. Evernode developer kit Internally interact with Evernode (via [XRPL](https://xrpl.org/)) to create Evernode instances and deploy your smart contracts on the [Evernode network](https://dashboard.evernode.org).

You have learned how to test your Smart Contracts locally with `hpdevkit` in the [hpdevkit basic tutorial](../hotpocket/tutorial-basics.md). Now let's see how you can acquire instances from Evernode and deploy smart contract you've implemented. First you need to have an XRPL account with EVRs for the tenant.

## Install Evernode developer kit

Follow [installation instructions](../evdevkit//index.md#installation) to install Evernode developer kit on your PC.

## Acquire an instance

- First you need to have a tenant XRPL account. You can generate an XRPL faucet account with 6000 EVRs on [hooks-testnet-v2](https://xrpl-hooks.readme.io/) from [here](https://dashboard.evernode.org/#/testnet-faucet).

1. Then you need to prepare a user key pair for the Evernode instance.

- Run below command to prepare a new key pair.
  ```bash
  evdevkit keygen
  ```
- This will output the generated key pair as follows.
  ```bash
  New key pair generated {
     privateKey: 'ed797ecd191b0364db559896c648c21cda7763db551a97577ed9ffb0ebb41881d8f9d1af6ff29af9287b0411758aac472016fb186220ef39db7959294c28857909',
     publicKey: 'edf9d1af6ff29af9287b0411758aac472016fb186220ef39db7959294c28857909'
  }
  ```

2. Now you need to set [environment variables](../evdevkit/index.md#environment-variables) for tenant secret and user private key.

   ```bash
   # Windows (command prompt)
   set EV_TENANT_SECRET=snmyH19JLWVaUJKtM4cNxTT6t38eA
   set EV_USER_PRIVATE_KEY=ed797ecd191b0364db559896c648c21cda7763db551a97577ed9ffb0ebb41881d8f9d1af6ff29af9287b0411758aac472016fb186220ef39db7959294c28857909

   # Windows (powershell)
   $env:EV_TENANT_SECRET=snmyH19JLWVaUJKtM4cNxTT6t38eA
   $env:EV_USER_PRIVATE_KEY=ed797ecd191b0364db559896c648c21cda7763db551a97577ed9ffb0ebb41881d8f9d1af6ff29af9287b0411758aac472016fb186220ef39db7959294c28857909

   # Linux (bash)
   export EV_TENANT_SECRET=snmyH19JLWVaUJKtM4cNxTT6t38eA
   export EV_USER_PRIVATE_KEY=ed797ecd191b0364db559896c648c21cda7763db551a97577ed9ffb0ebb41881d8f9d1af6ff29af9287b0411758aac472016fb186220ef39db7959294c28857909
   ```

3. Let's override the HotPocket configuration in the Evernode instance. You can skip this step if you do not want to.

   - Create a new json file anywhere you prefer.
   - Add following content inside the file.
     ```json
     {
       "contract": {
         "consensus": {
           "roundtime": 6000
         }
       },
       "mesh": {
         "peer_discovery": {
           "enabled": true,
           "interval": 10000
         }
       }
     }
     ```
   - See [Hotpocket configuration reference](/hotpocket/reference-configuration.md) for more details.
   - Now set the json file path as `EV_INSTANCE_CONFIG_PATH` [environment variable](../evdevkit/index.md#environment-variables).

     ```bash
     # Windows (command prompt)
     set EV_INSTANCE_CONFIG_PATH=<Path to your HotPocket instance configuration file>

     # Windows (powershell)
     $env:EV_INSTANCE_CONFIG_PATH=<Path to your HotPocket instance configuration file>

     # Linux (bash)
     export EV_INSTANCE_CONFIG_PATH=<Path to your HotPocket instance configuration file>
     ```

4. Now you are ready to acquire an Evernode instance.

   - Run following command to acquire.
     ```bash
     evdevkit acquire
     ```
   - This will create an Evernode instance in a random host and outputs the instance details
     ```bash
     ...
     Instance created! {
        name: '00010000414A6FB83385362D61A89FDE24414914AE05FCB8AACE82C400000029',
        ip: '66.42.63.218',
        pubkey: 'edbf186b1bbedf0a3e7351ec969d0aeee40b45831fd924db0a129663ec5920992d',
        contract_id: '020e552d-11f9-4707-a8c0-eecd6f4289da',
        peer_port: '22861',
        user_port: '8081'
     }
     ```
   - You can specify more options (Ex: `-h` specify a host) to the `acquire` command. Check the supported options using the below command.
     ```bash
     evdevkit acquire --help
     ```

5. At this point you have acquired an Evernode instance successfully and you have the instance details including public key, IP, ports etc.

## Create deployable smart contract package

1. If you have already implemented smart contract package you can proceed, Otherwise go through [HotPocket developer kit tutorials](../hotpocket/tutorial-basics.md) and create a new smart contract.
2. Build your smart contract and note the build path.
3. Let's override the contract section HotPocket configuration in the Evernode instance. You can skip this step if you do not want to.

   - Create a new json file anywhere you prefer.
   - Add following content inside the file.
     ```json
     {
       "consensus": {
         "roundtime": 2000
       }
     }
     ```
   - See [Hotpocket contract configuration reference](/hotpocket/reference-configuration.md#contract) for more details.
   - Now set the json file path as `EV_CONTRACT_CONFIG_PATH` [environment variable](../evdevkit/index.md#environment-variables).

     ```bash
     # Windows (command prompt)
     set EV_CONTRACT_CONFIG_PATH=<Path to your contract configuration json file>

     # Windows (powershell)
     $env:EV_CONTRACT_CONFIG_PATH=<Path to your contract configuration json file>

     # Linux (bash)
     export EV_CONTRACT_CONFIG_PATH=<Path to your contract configuration json file>
     ```

4. Now you can create the smart contract package
   - Run the following command. Assuming this is a node contract last binary path and binary args parameters will be `/usr/bin/node` and `index.js`.
     ```bash
     evdevkit bundle $HOME/contract edbf186b1bbedf0a3e7351ec969d0aeee40b45831fd924db0a129663ec5920992d /usr/bin/node -a index.js
     ```
     - Note:
       - Replace `$HOME/contract` with your contract directory path (Path to build directory of contract binaries).
       - Replace `edbf186b1bbedf0a3e7351ec969d0aeee40b45831fd924db0a129663ec5920992d` with your instance's public key.
       - Replace `/usr/bin/node` `index.js` with your binary path and arguments.
   - This will bundle your smart contract and outputs the path to the zip file.
     ```bash
     ...
     Archive finished. (location: $HOME/bundle/bundle.zip)
     ```

## Deploy smart contract to the Evernode instance

1. Now you have the deployable contract bundle, We can deploy this bundle to the acquired Evernode instance.
2. `EV_USER_PRIVATE_KEY` is required for this step. Since we've already set this in [acquire instance](#acquire-an-instance) step, we do not need to set this again.

   - Make sure you use the same user keys which you've set when acquiring the instance, Otherwise you wont be allowed to upload this bundle.

3. Now let's upload the bundle to the Evernode instance.

   - Run the following command to upload the bundle.
     ```
     evdevkit deploy $HOME/bundle/bundle.zip 66.42.63.218 8081
     ```
   - This will deploy the contract and output following if successfully uploaded.
     ```
     ...
     Contract bundle uploaded!
     ```

4. You can test the uploaded contract by implementing a user client same as you did in [hpdevkit basic tutorial](../hotpocket/tutorial-basics.md#create-the-client-application).

## Acquire and deploy instance in one go

Using Evernode devkit you can acquire and deploy Evenode instances using one command.

1. First you need to follow the same 1, 2, 3 steps in ["Acquire an instance"](#acquire-an-instance) here as well.
2. If you are going to override HotPocket config contract section, follow 3rd step in ["Create deployable smart contract package"](#create-deployable-smart-contract-package).
3. Now you can just run the following command to acquire and deploy.
   - Run this command.
     ```bash
     evdevkit acquire-and-deploy $HOME/contract /usr/bin/node -a index.js
     ```
     - Note:
       - Replace `$HOME/contract` with your contract directory path (Path to build directory of contract binaries).
       - Replace `/usr/bin/node` `index.js` with your binary path and arguments.
   - This will output following after deploying the contract.
     ```bash
     ...
     Instance created! {
        name: '000100004B2CDD9D208422C913DFB2EDB880FCA30D653644920E65060000006B',
        ip: '107.173.83.83',
        pubkey: 'ede6d05dc23bf9aba9cabf6de65d280acc0ba3f2a356484af28b140a125308705d',
        contract_id: '7902171c-3d34-4c10-a5f7-d436e3525852',
        peer_port: '22861',
        user_port: '8081'
     }
     ...
     Archive finished. (location: $HOME/bundle/bundle.zip)
     ...
     Contract bundle uploaded!
     Contract deployed!
     ```
   - You can specify more options (Ex: `-h` specify a host) to the `acquire-and-deploy` command. Check the supported options using below command.
     ```bash
     evdevkit acquire-and-deploy --help
     ```

Next: [Deploying smart contract to Evernode cluster](tutorial-cluster.md)
