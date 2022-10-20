# Deploying a smart contract to an Evernode cluster

You can create an instance cluster in Evernode and deploy your [smart contract](../hotpocket/concepts.md#smart-contract). Like you did locally in ["HotPocket tutorial - multiple nodes"](/hotpocket/tutorial-multinode.md). Evernode developer kit provides you the facility to do this. Even though you can give a single command and create a up and running cluster with your contract running on it, It's important what it does internally when creating the cluster. So, these are the steps it follow.
1. Create a single node in one of the Evernode hosts.
   
2. Create rest of the nodes with only first nodes public key in it as [UNL](../hotpocket/concepts.md#unl---unique-node-list).
   
3. Created a contract bundle with your contract and HotPocket contract configuration with list of all the nodes public key as the [UNL](../hotpocket/concepts.md#unl---unique-node-list).
   
4. Upload the bundle to the first node.
   - The other nodes which are listening to the first node will receive the same bundle and all nodes will update their contracts themselves.
00010000414A6FB83385362D61A89FDE24414914AE05FCB8C1B453C50000002A
## Deploy smart contract
In Evernode developer kit, single command does the cluster creation and the deployment. But you need to do following preparations before same as you did in ["Acquire an instance"](tutorial-basics.md#acquire-an-instance)
- First you need to have a tenant XRPL account. You can generate an XRPL faucet account on [hooks-testnet-v2](https://xrpl-hooks.readme.io/) from [here](https://hooks-testnet-v2.xrpl-labs.com/).
1. Then you need to prepare a user key pair for the Evernode instance.
  - Run below command to prepare a new key pair.
      ```bash
      hpdevkit keygen
      ```
  - This will output the generated key pair as follows.
      ```bash
      New key pair generated {
         privateKey: 'ed797ecd191b0364db559896c648c21cda7763db551a97577ed9ffb0ebb41881d8f9d1af6ff29af9287b0411758aac472016fb186220ef39db7959294c28857909',
         publicKey: 'edf9d1af6ff29af9287b0411758aac472016fb186220ef39db7959294c28857909'
      }
      ```

2. Let's override the HotPocket configuration in the Evernode instance. You can skip this step if you do not want to but we recommend you to give some large value as `6000` to the `contract.consensus.roundtime` if you are going to deploy a large smart contract bundle.
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
   - Now set the json file path as `EV_HP_CONFIG_PATH` [environment variable](../evdevkit/index.md#environment-variables).
      ```bash
      # Windows (command prompt)
      set EV_HP_CONFIG_PATH=$HOME/hp.cfg.override
      
      # Windows (powershell)
      $env:EV_HP_CONFIG_PATH=$HOME/hp.cfg.override
      
      # Linux (bash)
      export EV_HP_CONFIG_PATH=$HOME/hp.cfg.override
      ```
      - Note: Replace `$HOME/contract.cfg.override` with your json file path.

3. Now let's override the contract section HotPocket configuration in the Evernode instance. You can skip this step if you do not want to but you can set the original roundtime you are intending to give for your smart contract in the `consensus.roundtime` section.
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
      set EV_CONTRACT_CONFIG_PATH=$HOME/contract.cfg.override
      
      # Windows (powershell)
      $env:EV_CONTRACT_CONFIG_PATH=$HOME/contract.cfg.override
      
      # Linux (bash)
      export EV_CONTRACT_CONFIG_PATH=$HOME/contract.cfg.override
      ```
      - Note: Replace `$HOME/contract.cfg.override` with your json file path.

4. Then you need to set [environment variables](../evdevkit/index.md#environment-variables) for tenant secret and user private key.
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

5. Now you are ready to deploy the smart contract to an Evernode instance.
   - Let's create a 5 node cluster. Run following command to create the cluster.
      ```bash
      evdevkit cluster-create 5 $HOME/contract edbf186b1bbedf0a3e7351ec969d0aeee40b45831fd924db0a129663ec5920992d /usr/bin/node -a index.js
      ```
   - Note:
        - `5` Is the cluster size.
        - Replace `$HOME/contract` with your contract directory path (Path to build directory of contract binaries).
        - Replace `edbf186b1bbedf0a3e7351ec969d0aeee40b45831fd924db0a129663ec5920992d` with your instance's public key.
        - Replace `/usr/bin/node` `index.js` with your binary path and arguments.
   - This will create a Evernode cluster in a random hosts and outputs the instance details
      ```bash
      ...
      Cluster created!
      ...
      Archive finished. (location: $HOME/bundle/bundle.zip)
      ...
      Contract bundle uploaded!
      Created the 5 node cluster!
      {
         "host": "rEqGKSwhfW8T2cM6HufcpPLvHjNHM85oJT",
         "name": "00010000A2B2838945C16363C73FAFD39E05E0F71657A7A00EA5D0B200000017",
         "ip": "45.76.238.97",
         "pubkey": "edb77acaaefa65e715a9a311bd567dd65c63643dac807d63e8090e2eb9433ef0c9",
         "contract_id": "587616dc-c0b6-4e23-980d-ef0b80711b11",
         "peer_port": "22861",
         "user_port": "26201"
      },
      {
         "host": "rnnXaSGFFti2U8i9psUbWoFqFYMfX4xzxT",
         "name": "000100002CBB49B760068D7AA6424291CC5F5A0F10508582493CED0E00000073",
         "ip": "198.71.52.214",
         "pubkey": "eddc20f26cddc5f9213791f98959ede7a76a79f3e413fb899444952ac5b31b89d8",
         "contract_id": "587616dc-c0b6-4e23-980d-ef0b80711b11",
         "peer_port": "22875",
         "user_port": "26215"
      },
      {
         "host": "r3tSGeDFJaz8GEVmM6oUuYTAiNdDJhitCt",
         "name": "0001000056833048A72B65388ABDBE854F65324D7786A9EA6C82CCEE00000053",
         "ip": "207.148.120.201",
         "pubkey": "ed22f23cae892eb163c44163be6c94f7a1aab9ef21ea27072165fd14d70d29ab25",
         "contract_id": "587616dc-c0b6-4e23-980d-ef0b80711b11",
         "peer_port": "22862",
         "user_port": "26202"
      },
      {
         "host": "rwyD9X6G5ejd5DJW5WpxWvCrHFGxH2nYWW",
         "name": "000100006D5A90D600448ED25674DD43029C2412C15150E544B17C9E00000003",
         "ip": "139.144.79.138",
         "pubkey": "ed71c2b5582da62a99909ed141d759d476d3c626b61f325acfd5f90ccb828586dd",
         "contract_id": "587616dc-c0b6-4e23-980d-ef0b80711b11",
         "peer_port": "22861",
         "user_port": "26201"
      },
      {
         "host": "rBWAXbwQSGwYg5NPmdVWsQkXTS7fdRhCXL",
         "name": "00010000735AD68125616A771D288703D5335E2503B281877D02E0C200000027",
         "ip": "79.173.139.104",
         "pubkey": "ed01f60875531c364382e204ad14ffbcf444d9d37cebe4797f0c7b832131377dec",
         "contract_id": "587616dc-c0b6-4e23-980d-ef0b80711b11",
         "peer_port": "22870",
         "user_port": "26209"
      }
      ```
   - You can specify more options (Ex: `-h` specify a host) to the `cluster-create` command. Check the supported options using the below command.
      ```bash
      evdevkit cluster-create --help
      ```
   
6. At this point you have created a Evernode cluster successfully and you have the instance details including public key, IP, ports etc. Now you can test the uploaded contract by implementing a user client same as you did in [hpdevkit basic tutorial](../hotpocket/tutorial-basics.md#create-the-client-application).