# HotPocket tutorial

HotPocket smart contract development involves two areas. Developing the smart contract which contains the logic of your application, and developing user-facing client application that interact with the smart contract. HotPocket acts as the middleman of bridging your client applications with the smart contract.

HotPocket runs only on Linux (Ubuntu 20.04) and is capable of using Linux POSIX-compliant applications as smart contracts. HotPocket uses [Docker](https://www.docker.com/) to bundle HotPocket and your smart contracts into a runnable Linux environment. Therefore, you can carry out your development activities on platforms such as Windows with the help standard cross-platform programming environments.

This guide explains the journey of developing a smart contract and a client application using NodeJs. Therefore it'll help you if you have prior knowledge of developing NodeJs applications. We will also use [HotPocket developer kit](hpdevkit.md) throughout this guide so most of the setup work like HotPocket and docker container configurations are done for you automatically. Therefore you can carry out the steps on this guide even if you do not have prior NodeJs experience.

## 1. Install HotPocket developer kit

Follow [Windows](hpdevkit.md#windows-installation) or [Linux](hpdevkit.md#linux-installation) instructions to install HotPocket developer kit on your PC.

## 2. Create a blank smart contract

Generate a blank smart contract project with: `hpdevkit gen nodejs blank-contract mycontract`. It will create a folder named 'mycontract' with contract files. Let's have a look at them.

#### src/mycontract.js

This is the entry point of your smart contract. As shown below, `hpc.init()` takes care of registering the provided function as the entry point to your smart contract and setting up the communication channels between your nodejs application and HotPocket.

```javascript
const HotPocket = require("hotpocket-nodejs-contract");

const myContract = async (ctx) => {
  // Your smart contract logic.
  console.log("Blank contract");
};

const hpc = new HotPocket.Contract();
hpc.init(myContract);
```

During normal operation, HotPocket will invoke your application and pass the relevant information into the `ctx` argument of your contract function. This is called the 'contract context' which contains information about that execution of the contract. We will explore this later in this guide.

#### dist/hp.cfg.override

This file contains configuration specific to your smart contract. HotPocket supports many other configuration parameters but here we are only specifying `bin_path` and `bin_args` parameters. HotPocket will use the default values for any parameters which haven't been specified. For the purpose of this guide, we will not discuss about other parameters that are not shown here.

```json
{
  "contract": {
    "bin_path": "/usr/bin/node",
    "bin_args": "index.js"
  }
}
```

`bin_path` indicates to HotPocket the location of the Linux application binary to be executed. Since we have generated a NodeJs application using HotPocket developer kit, it indicates the NodeJs install location within the HotPocket nodejs docker image.

`bin_args` specifies any arguments (space separated) to be passed to binary. In this case we are passing the final compiled script of our javascript application into nodejs.

## 4. Run the smart contract

Run the generated NodeJs smart contract application by navigating into the 'mycontract` directory and running the following commands.

```
cd mycontract
npm install
npm start
```

This will make HotPocket developer kit to build and deploy your smart contract into a single HotPocket instance. At the end, it will start showing some console output emitted by Hot Pocket like this:

```
20220821 09:23:50.213 [inf][hpc] ****Ledger created**** (lcl:1-a80e9b9d state:aab8e909 patch:7365a671)
Blank contract
20220821 09:23:51.209 [inf][hpc] ****Ledger created**** (lcl:2-55e988d9 state:aab8e909 patch:7365a671)
Blank contract
20220821 09:23:52.209 [inf][hpc] ****Ledger created**** (lcl:3-835c39b2 state:aab8e909 patch:7365a671)
Blank contract
```

You can press ctrl+C to exit from logging output. The HotPocket instance will continue to run. To revisit the log you can use the command `hpdevkit logs 1`.

_Here, command '1' is the instance (node) number. By default HotPocket devveloper kit creates only one instance but it's capable of creating a cluster of multiple HotPocket nodes inside your PC. For now, let's stick with this simple single-node setup. See [advanved usage](hpdevkit.md#advanced-usage) of HotPocket developer kit for more info._

### 4.1. Consensus and contract execution

You will notice that the above log prints an execution log every few seconds. This is due to the way HotPocket operates and how it executes your smart contract. HotPocket uses an interval called 'consensus roundtime' which controls how often it attempts to exchange information with other HotPocket nodes in the cluster and arrive at consensus. At the end of every round, HotPocket creates a ledger using the information that was subjected to consensus. It then executes your smart contract and passes it the information corresponding to the ledger that was just created.

Now, with this understanding, let's revisit the code in 'mycontract.js'.

```javascript
const HotPocket = require("hotpocket-nodejs-contract");

const myContract = async (ctx) => {
  // Your smart contract logic.
  console.log("Blank contract");
};

const hpc = new HotPocket.Contract();
hpc.init(myContract);
```

`hpc.init()` will cause HotPocket to invoke `mycontract` function whenever it creates a ledger. HotPocket will pass the `ctx` argument containing any information that it believes to be 'universal' among all the HotPocket nodes in the cluster. HotPocket will not invoke your application/function if it couldn't reach consensus (hence, no ledger was created) during a particular consensus round.

### 4.2. Updating the contract

Currently, our contract simply prints 'Blank contract'. Let's update it to print some information from the contract context passed by HotPocket as follows.

```javascript
const HotPocket = require("hotpocket-nodejs-contract");

const myContract = async (ctx) => {
  // Your smart contract logic.
  console.log("Ledger number", ctx.lclSeqNo);
  console.log("Connected users", ctx.users.count());
};

const hpc = new HotPocket.Contract();
hpc.init(myContract);
```

Now, run `npm start` again. This will rebuild and redeploy your contract and show the console output.

```
20220821 10:01:53.215 [inf][hpc] ****Ledger created**** (lcl:1-d526d397 state:8a2aee45 patch:7365a671)
Ledger number 1
Connected users 0
20220821 10:01:54.211 [inf][hpc] ****Ledger created**** (lcl:2-f39ab881 state:8a2aee45 patch:7365a671)
Ledger number 2
Connected users 0
20220821 10:01:55.212 [inf][hpc] ****Ledger created**** (lcl:3-6617ef82 state:8a2aee45 patch:7365a671)
Ledger number 3
Connected users 0
```
You can see now we are printing the ledger number passed from HotPocket inside our smart contract. We are also printing no. of users connected to our smart contract but obviously it's returning 0. If there were any connected users, we could write logic in our contract to process any data sent by the users and do some useful things with it. In the next section let's see how users can connect to our contract.

Press ctrl+C to stop the console output. HotPocket will continue to run in the background (in order to terminate it, you can use the command `hpdevkit stop`).

This is how you would keep on updating and testing your contract on your local PC.

## 5. Connect users
In order to interact with the smart contract, we must create a HotPocket client application which can connect to HotPocket as a user.