# Deploying a smart contract to an Evernode cluster

Using Evernode developer kit, you can create an instance cluster in Evernode and deploy your [smart contract](../hotpocket/concepts.md#smart-contract), like you did locally in ["HotPocket tutorial - multiple nodes"](/hotpocket/tutorial-multinode.md). Even though you can give a single command and create an up and running cluster with your [smart contract](../hotpocket/concepts.md#smart-contract) running on it, it's important to have an understanding about what it does internally when creating the cluster. So, these are the steps it follow.

1. Creates a single node in one of Evernode hosts.
2. Creates rest of the nodes with only first node's public key in their [UNL](../hotpocket/concepts.md#unl---unique-node-list).
3. Creates a contract bundle with your smart contract binaries and HotPocket contract configuration containing list of all the nodes public key as the [UNL](../hotpocket/concepts.md#unl---unique-node-list).
4. Upload the bundle to the first node.
   - The other nodes which are listening to the first node will receive the same bundle and all nodes will update their contracts themselves.

## Deploy smart contract

In Evernode developer kit, single command does the cluster creation and the deployment. You need to do following preparations before same as you did in ["Acquire an instance"](tutorial-basics.md#acquire-an-instance)

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

3. Let's override the HotPocket configuration in the Evernode instance. You can skip this step if you do not want to but we recommend you to give some large value as `6000` to the `contract.consensus.roundtime` if you are going to deploy a large smart contract bundle.

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
     set EV_INSTANCE_CONFIG_PATH=<Path to your instance configuration json file>

     # Windows (powershell)
     $env:EV_INSTANCE_CONFIG_PATH=<Path to your instance configuration json file>

     # Linux (bash)
     export EV_INSTANCE_CONFIG_PATH=<Path to your instance configuration json file>
     ```

4. Now let's override the contract section of the HotPocket configuration in the Evernode instance. This section will be identical in each HotPocket instance in the cluster as the contract configuration is synchronized within the cluster. If you do not want, you can skip this step as well. However you can set the original `roundtime` you are intending to give for your smart contract here in the `consensus.roundtime` section.

   - Create a new json file anywhere you prefer.
   - Add following content inside the file.
     ```json
     {
       "consensus": {
         "roundtime": 2000
       }
     }
     ```
   - See [HotPocket contract configuration reference](/hotpocket/reference-configuration.md#contract) for more details.
   - Now set the json file path as `EV_CONTRACT_CONFIG_PATH` [environment variable](../evdevkit/index.md#environment-variables).

     ```bash
     # Windows (command prompt)
     set EV_CONTRACT_CONFIG_PATH=<Path to your contract configuration json file>

     # Windows (powershell)
     $env:EV_CONTRACT_CONFIG_PATH=<Path to your contract configuration json file>

     # Linux (bash)
     export EV_CONTRACT_CONFIG_PATH=<Path to your contract configuration json file>
     ```

5. Now you are ready to deploy the smart contract to an Evernode cluster.
   - Let's create a 5 node cluster. Run following command to create the cluster.
     ```bash
     evdevkit cluster-create 5 $HOME/contract /usr/bin/node -a index.js
     ```
   - Note:
     - `5` Is the cluster size.
     - Replace `$HOME/contract` with your contract directory path (Path to build directory of contract binaries).
     - Replace `/usr/bin/node` `index.js` with your binary path and arguments.
   - This will create an Evernode cluster in a random hosts and outputs the instance details.
     ```bash
     ...
     Cluster created!
     ...
     Archive finished. (location: $HOME/bundle/bundle.zip)
     ...
     Contract bundle uploaded!
     Created the 5 node cluster! [
        {
           host: 'rDKgSroMoh5Ur1EDxFZnGJXzk2MFeDg3ts',
           name: '000100008729E98B90DCE8BFE77867DBFE2FE31193169B059699D576000000DB',
           ip: 'evernode.xumm.dev',
           pubkey: 'ed0b77366a0202a1573d83ea3c18f73bda23592f190165abb9e82ee03379978014',
           contract_id: 'eb051608-c916-4f48-a8dc-a906b6e1f19d',
           peer_port: '22861',
           user_port: '8081'
        },
        {
           host: 'rKRjgkwZABgh6e38cES7aor6cLjFETkpBA',
           name: '00010000CA1FE108B76979D1557CE80B7BD8103E5942D26893E8B1C300000028',
           ip: '45.32.197.83',
           pubkey: 'ed3bd71ef91da28eb4e704ca41618a059e2effabd064c49b226d2edec6cbc5b40b',
           contract_id: 'eb051608-c916-4f48-a8dc-a906b6e1f19d',
           peer_port: '22861',
           user_port: '26201'
        },
        {
           host: 'rn49hoLXBY2AnZG4d2EvqVQGLfWaExh9eE',
           name: '000100002FDFF898274403F4C3A2256611FC885C956ACF32AACE82C400000029',
           ip: '149.28.92.171',
           pubkey: 'ed0d1f25e338cc9531130d5c590bdd118b677eec9d4796868128543aaad1ccdbdf',
           contract_id: 'eb051608-c916-4f48-a8dc-a906b6e1f19d',
           peer_port: '22861',
           user_port: '26201'
        },
        {
           host: 'rBWAXbwQSGwYg5NPmdVWsQkXTS7fdRhCXL',
           name: '00010000735AD68125616A771D288703D5335E2503B28187FA05B7E90000004E',
           ip: '79.173.139.104',
           pubkey: 'ed1f802792d9e5b4dc8b9a73d93689fb9321a993afdb8077f91611695137be87cd',
           contract_id: 'eb051608-c916-4f48-a8dc-a906b6e1f19d',
           peer_port: '22861',
           user_port: '26200'
        },
        {
           host: 'rKQyXCJGv1B3cqkpykvHYM97H2mVSqJkP9',
           name: '00010000C9FB04D9E46A6F320A4533AB0AD69DE8C15391100000099B00000000',
           ip: '172.104.131.188',
           pubkey: 'edaa2f1705f2182e49902544a2ff1433dd745185b42c4857fd7b244eace86e0da1',
           contract_id: 'eb051608-c916-4f48-a8dc-a906b6e1f19d',
           peer_port: '22861',
           user_port: '26201'
        }
     ]
     ```
   - You can specify more options (Ex: `-h` specify a host) to the `cluster-create` command. Check the supported options using below command.
     ```bash
     evdevkit cluster-create --help
     ```
6. At this point you have created an Evernode cluster successfully and you have details of all the instances including public key, IP, ports etc. Now you can test the uploaded contract by implementing a user client same as you did in [hpdevkit basic tutorial](../hotpocket/tutorial-basics.md#create-the-client-application).
