# Evernode basics tutorial

You have learned how to test your Smart Contracts locally with hpdevkit in the [hpdevkit basic tutorial](../hotpocket/tutorial-basics.md). Now let's see how you can acquire instances from Evernode and deploy smart contract you've implemented. To be a tenant and deploy a smart contract in the Evernode network you'll have to follow following steps.
- **Step 1** - First, you have to have an XRPL account.
- **Step 2** - You need to have EVRs on your XRPL account. (Since this is the Beta network, we will be giving away free EVRs for you to test smart contracts on Evernode)
- **Step 3** - You need to pick a host and acquire a lease from the picked host.
- **Step 4** - Initially with the amount you paid for the lease you'll get an instance with life time of 1 Moment (1190 XRP ledgers) approximately 1 hour. You can extend the instance life time by extending the lease.
- **Step 5** - You need prepare starter contract client and user keys before acquiring the instance.
- **Step 6** - Now as the final step, You can prepare your smart contract bundle and deploy your smart contract.

Let's go through them in detail.

## Generate and prepare tenant account

### Step 1 - Tenant account preparation.
First, You need to create an XRPL account and start the tenant client preparation.
1. You can generate an XRPL faucet account on [hooks-testnet-v2](https://xrpl-hooks.readme.io/) from [here](https://hooks-testnet-v2.xrpl-labs.com/).
2. Go to your work directory and create a new directory `evernode-project` for your Evernode test.
3. Go to the project directory and run `npm init` to prepare a npm project.
4. Now run,
   ```
   npm i evernode-js-client
   ```
   to install `evernode-js-client` to your project.
5. Open the project directory with your favorite text editor and create `index.js` file with following content.
   - Fill `<tenant_address>` and `<tenant_secret>` with your generated account info from the [faucet](https://hooks-testnet-v2.xrpl-labs.com/).
   ```javascript
      const evernode = require("evernode-js-client");

      const tenantCredentials = {
         address: '<tenant_address>',
         secret: '<tenant_secret>'
      }

      async function app() {
         // Content goes here.
      }

      app().catch(console.error).finally(() => { process.exit(0) });
   ```
6. Now fill in the content inside the `app()` function.
   - Setup the XRPL API and registry account info.
   ```javascript
      const xrplApi = new evernode.XrplApi('wss://hooks-testnet-v2.xrpl-labs.com');
      evernode.Defaults.set({
         registryAddress: 'r3cNR2bdao1NyvQ5ZuQvCUgqkoWGmgF34E',
         xrplApi: xrplApi
      });
   ```
7. Let's create the tenant client.
   - First we create the tenant client instance.
   - Then call the `connect()` method for internal preparations and establish the XRPL connection.
   - Then call the `prepareAccount()` method to setup the XRPL account keys for message encryption between tenant and evernode Host.
   ```javascript
      const tenantClient = new evernode.TenantClient(tenantCredentials.address, tenantCredentials.secret);
      await tenantClient.connect();
      await tenantClient.prepareAccount();
   ```

### Step 2 - Request BETA EVR funds.
Now the tenant client preparation part is done. As mentioned above you need to have EVRs in your account to acquire Evernode instances.
1. Let's add following code to request EVRs.
   - Your tenant account will be awarded 100 EVRs for the beta testing.
   ```javascript
      // Get the available EVR balance.
      let balance = await tenantClient.getEVRBalance();
      // Request EVRs if we do not have enough.
      if (balance < 4) {
         // Create the trustline if not created.
         const lines = await tenantClient.xrplAcc.getTrustLines(evernode.EvernodeConstants.EVR, tenantClient.config.evrIssuerAddress);
         if (!lines || lines.length === 0)
            await tenantClient.xrplAcc.setTrustLine(evernode.EvernodeConstants.EVR, tenantClient.config.evrIssuerAddress, "99999999");

         // Send the EVR request transaction.
         await tenantClient.xrplAcc.makePayment(tenantClient.config.foundationAddress,
            evernode.XrplConstants.MIN_XRP_AMOUNT,
            evernode.XrplConstants.XRP,
            null,
            [{ type: 'giftBetaTenantEvr', format: '', data: '' }]);

         // Wait until the EVRs are received.
         let attempts = 0;
         while (attempts >= 0) {
            await new Promise(resolve => setTimeout(resolve, 1000));
            balance = await tenantClient.getEVRBalance();
            if (balance < 4) {
                  if (++attempts <= 20)
                     continue;
                  throw "EVR funds not received within timeout.";
            }
            break;
         }
      }
   ```
### Step 3 - Pick an active available host to acquire.
1. So, Now you have a tenant account with enough EVRs. Let's pick a host to acquire an instance.
   - List down all the active hosts with available slots. _(You can't acquire from inactive Hosts)_
   - First, you need to instantiate a registry client.
   - Then call the `connect()` method on the registry client to prepare.
   ```javascript
      const registryClient = new evernode.RegistryClient();
      await registryClient.connect();
      // Get only the hosts which has available slots.
      const hosts = (await registryClient.getActiveHosts()).filter(h => h.maxInstances - h.activeInstances > 0);
      // Pick a random host.
      const pickedHost = hosts[Math.floor(Math.random() * hosts.length)];
   ```
2. Let's acquire an instance from the picked host.
   - We'll take the user `owner_pubkey` from command line arguments which we'll be generating in the [Step 5](#step-5---acquire-evernode-instance).
   ```javascript
      let instanceName;
      try {
         const result = await tenantClient.acquireLease(pickedHost.address, {
            owner_pubkey: process.argv[2],
            contract_id: "dc411912-bcdd-4f73-af43-32ec45844b9a",
            image: "hp.latest-ubt.20.04-njs.16",
            config: {}
         });
         console.log('Tenant received instance ', result.instance);
         instanceName = result.instance.name;
      }
      catch (err) {
         console.log("Tenant received acquire error: ", err.reason);
      }
   ```

### Step 4 - Extend instance life.
1. Now you have purchased an instance for one moment (Approx 1 hour). Let's extend the instance life for another moment (Approx 1 hour).
   ```javascript
      if (instanceName) {
         try {
            const result = await tenantClient.extendLease(pickedHost.address, 2, instanceName);
            console.log(`Extend ref id: ${result.extendeRefId}, Expiry moments: ${result.expiryMoment}`);
         }
         catch (err) {
            console.log("Tenant received extend error: ", err.reason)
         }
      }
   ```
2. Disconnect everything at the end.
   ```javascript
      await tenantClient.disconnect();
      await registryClient.disconnect();
      await xrplApi.disconnect();
   ```

### Step 5 - Acquire evernode instance.
1. Here, you need to have an `owner_pubkey` which we've taken as command line arguments in [Step 3](#step-3---pick-an-active-available-host-to-acquire) beforehand running the acquire program.
   - `owner_pubkey` is the public key of the user who is going to deploy the contract to the acquired evernode instance.
   - So, to generate such a key we will first create a HotPocket client which is going to connect to the acquired instance later.
   - For that we are going to use [hpdevkit](../hpdevkit/index.md). So, make sure you've installed [hpdevkit](../hpdevkit/index.md) on your machine. And followed [hpdevkit basic tutorial](../hotpocket/tutorial-basics.md) for easiness of understanding.
   - Assuming you are currently in the `evernode-project` directory, Go back to parent directory and run following command to create `evernode-starter-client`.
   ```
   hpdevkit gen nodejs evernode-starter-client myclient
   cd myclient
   npm install
   ```
   - You can open the client project with your code editor and look in the `myclient.js` file using your previous understanding on [hpdevkit basic tutorial](../hotpocket/tutorial-basics.md) and try to understand the code.
   - The initial Evernode instance which is going to be acquired, is running a starter contract which is called `bootstrap contract` which will replace itself with the new [smart contract](../hotpocket/concepts.md#smart-contract) uploaded by the user. So, this `evernode-starter-client` is the client program for the bootstrap contract.
   - Let's generate a user keys for the client, run following command.
   ```
   node myclient.js generatekeys
   ```
   - This will output as follows, You can see your client public key in the output. We are going to use this public key in the next point.
   ```
   New key pair generated.
   My public key is: ed76534b66f1210d72669af15a95ac9ba96e3f2f9d6e689ee8c161eb0d309a39b2
   ```

2. You've generated the `evernode-starter-client` client now. So, let's go back to our previous `evernode-project` directory to acquire the Evernode instance.
   - Now run, following command to acquire the instance. (Replace the public key with the public key in your previous output.)
   ```
   # node index.js <client public key you've just generated>
   node index.js ed76534b66f1210d72669af15a95ac9ba96e3f2f9d6e689ee8c161eb0d309a39b2
   ```
   - You'll receive an output like follows.
  >There, you can see the instance details are provided in the payload. The field `publicKey` denotes the public key of the acquired instance. These values will be needed in the next step ([Step 6](#step-6---smart-contract-deployment)). Hence, keep them noted, if you clean your terminal.
   ```
   Transaction result: tesSUCCESS
   Waiting for acquire response... (txHash: 58F95FACA7A5963ED4A7A2D44943D6D54E0A8452DA6649A465213C265F0410C2)
   Tenant received instance  {
      name: '000100002364FCDF2D56EB7A9089E7938791F07008B66255A048C0A200000007',
      ip: '62.171.133.143',
      pubkey: 'ede673726c2b5c8eb9c14af633b6b51613b1f1fc3266aea9877a6b99a996b33df9',
      contract_id: 'dc411912-bcdd-4f73-af43-32ec45844b9a',
      peer_port: '22865',
      user_port: '26205'
   }
   Transaction result: tesSUCCESS
   Waiting for extend lease response... (txHash: 8263959C09469952B06A39D966DA79E9C60224DD06F1E2D5677F69D66DDDF710)
   Extend ref id: 8263959C09469952B06A39D966DA79E9C60224DD06F1E2D5677F69D66DDDF710, Expiry moments: 5290
   ```

### Step 6 - Smart contract deployment.
1. Now you have an Evernode instance, which is ready to be deployed a new [smart contract](../hotpocket/concepts.md#smart-contract). So, let's deploy the contract to the Evernode instance.
2. For the easiness of understanding let's upload the smart contract that you've created before in [hpdevkit basic tutorial](../hotpocket/tutorial-basics.md).
   - If you haven't followed the hpdevkit tutorial, It guides you how to generate a simple [smart contract](../hotpocket/concepts.md#smart-contract) using hpdevkit and test it locally.
   - So, here we upload that same simple smart contract to Evernode instance.
3. First, we need to prepare a smart contract bundle which can be uploaded to Evernode instance via the [evernode-starter-client](#step-3---pick-an-active-available-host-to-acquire).
   - Go to the smart contract directory which you've implemented in [hpdevkit basic tutorial](../hotpocket/tutorial-basics.md) and run following command to generate the contract bundle.
   - Here, you have to provide the public key of the acquired Evernode instance, which you have received at the end of the previous step ([Step 5](#step-5---acquire-evernode-instance)).
   ```
   cd mycontract
   npm install
   npm run build
   # hpdevkit bundle <acquired-node-public-key> <contract-path>
   hpdevkit bundle ede673726c2b5c8eb9c14af633b6b51613b1f1fc3266aea9877a6b99a996b33df9 dist
   ```
   Output will be,
   ```
   command: bundle
   Prepared contract.config file.
   Added prerequisite installer script.
   Archive finished. (location: /home/userx/smart-contracts/mycontract/bundle/bundle.zip)
   ```

4. Now the bundle is generated. Lets connect the previously created `evernode-starter-client` to the acquired the Evernode instance.
   - Go to the myclient project directory which is created in the [Step 5](#step-5---acquire-evernode-instance).
   - Pass the `ip` and the `user_port` received from the instance acquire response in [Step 5](#step-5---acquire-evernode-instance). (Replace the `ip` and `user_port` with your values received in the response.)
   ```
   cd myclient
   # node myclient.js <instance ip> <instance user port>
   node myclient.js 62.171.133.143 26205
   ```
   - Client will start outputting.
   ```
   Connecting to wss://62.171.133.143:26205
   Connected to wss://62.171.133.143:26205
   HotPocket Connected.
   Ready to accept inputs.
   ```

5. Check the starter contract status by typing below command in myclient programs terminal.
   ```
   status
   ```
   - If `bootsrap contract` is running, It'll output.
   ```
   (ledger:29)>> Bootstrap contract is online
   ```

6. Then enter the below command to upload the bundle zip file.
   ```
   upload <path to bundle zip file>
   ```
   - If the upload is successful it'll print.
   ```
   Uploading file bundle.zip (7 KB)
   (ledger:145)>> uploadSuccess
   ```

7. You can test the uploaded contract by implementing a user client same as you did in [hpdevkit basic tutorial](../hotpocket/tutorial-basics.md), when testing the local smart contracts.