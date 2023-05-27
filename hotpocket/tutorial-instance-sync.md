# HotPocket instance synchronization

HotPocket uses a synchronization mechanism which allows the nodes in the cluster to compare with and sync with each other. This allows the entire cluster to maintain a canonical state which has the support of the majority of the UNL nodes. In [node recovery](tutorial-multinode.md#node-recovery) and [persisting data](tutorial-persistdata.md) tutorials, it was shown how the filesystem "state" stays in-sync between all the nodes.

In addition to filesystem "state" sync, HotPocket also handles syncing of contract configuration as well. This means the configuration used to setup the contract must also be the same across the cluster. The combination of "state" and configuration sync, provides a powerfull way for a cluster to create newly cloned instances of itself. This allows a new blank HotPocket node to become a part of an existing contract cluster with a minimul set of information. Let's demonstrate that using a simple contract and `hpdevkit`.

## Create a new smart contract

1. Run `hpdevkit clean` to make sure any previous clusters are purged. This article assumes your hpdevkit cluster size is 3 (default size).
2. Run `hpdevkit gen nodejs blank-contract colorscontract` to create a new contract for this tutorial.
3. Navigate to 'colorscontract' directory and run `npm install`.

Make following changes to the generated files:

### src/colorscontract.js

```javascript
const HotPocket = require("hotpocket-nodejs-contract");
const process = require("process");

const mycontract = async (ctx) => {
  // Print the cli args passed to our application.
  console.log("These are my cli args", process.argv.splice(2));

  // Indicate whether this node's public key is in the UNL.
  if (ctx.unl.find(ctx.publicKey)) console.log("I'm in the UNL.");
  else console.log("I'm NOT in the UNL. My public key:", ctx.publicKey);
};

const hpc = new HotPocket.Contract();
hpc.init(mycontract);
```

The colors contract is using nodejs "process" module to print the command line arguments passed to the app. It is also stating that the node executing the contract is a part of the UNL by checking whether the node's public key is included in the UNL public key list.

In order to pass CLI arguments to our app, we need to edit the contract configuration using `hp.cfg.override` file.

### hp.cfg.override

```json
{
  "contract": {
    "bin_path": "/usr/bin/node",
    "bin_args": "index.js red green blue"
  }
}
```

As you can see we are passing 'red green blue' as arguments to index.js which represents the nodejs script for our contract at run time.

Run `npm start`. As expected, you will see following output from **node 1** which is showing the colors we are passing in "bin_args" property of our contract configuration. It is also indicating that the node 1 is in fact a part of the UNL. This is because when hpdevkit creates a cluster, it configures all the nodes to be in each other's UNL.

```
20230527 00:58:46.650 [inf][hpc] ****Ledger created**** (lcl:1-f192e9d1 state:3c90183b patch:35968a27)
These are my cli args [ 'red', 'green', 'blue' ]
I'm in the UNL.
20230527 00:58:47.645 [inf][hpc] ****Ledger created**** (lcl:2-bbf23bc9 state:3c90183b patch:35968a27)
These are my cli args [ 'red', 'green', 'blue' ]
I'm in the UNL.
```

Press Ctrl+C to exit from the log output.

## Spawn a new node

What we are going to do now is to create a completely new/blank node which does not have any files/configuration from the colors contract cluster but still get it to become a clone of that cluster eventually. To make this work, the new node must have the following information:

1. The [contract id](contract-context.md#contractid) of the existing contract.
2. The IP/domain address and the peer port of at least one of the existing nodes.
3. At least one public key of the existing UNL nodes (this does not ncessaraily need to be the same node as [2]).

When the new node is created, it is capable of using the limited information available to it and discover the existing contract cluster and sync with it. So the expectation is for the new node to eventually behave exactly the same as existing nodes and execute the same contract code.

However, even if the new node becomes a "clone", it should be noted that the existing nodes will not know about the new node and hence will not include it in their UNL. We should be able to see this via the new node's contract log output indicating its UNL status.

Run the command `hpdevkit spawn`. This simulates a new/blank node (**node 4**) being created with only the aforementioned information and displays its log output.

```
...
20230527 00:58:59.212 [inf][hpc] Hpfs ldgr sync: Achieved target:54bad461 /primary/0
These are my cli args [ 'red', 'green', 'blue' ]
I'm NOT in the UNL. My public key: edecee17cf622edfe35a6850c462ed86bde9952ecf2179d244f2fb1806b77903aa
20230527 00:59:00.646 [inf][hpc] ****Ledger created**** (lcl:15-a7ef4035 state:3c90183b patch:35968a27)
These are my cli args [ 'red', 'green', 'blue' ]
I'm NOT in the UNL. My public key: edecee17cf622edfe35a6850c462ed86bde9952ecf2179d244f2fb1806b77903aa
...
```

As you can see the new node is capable of requesting canonical configuration and "state" from the existing cluster and become a "clone". The contract code files (in this case, "index.js") also lives in the filesystem "state" hence we get a copy of the contract code itself. The key difference is that the new node is NOT in the cluster UNL as shown by its log output.

## UNL acceptance logic

At this point, it is up to you to build up the ncessary governance mechanism on how would a new node get "accepted" into the UNL. You must device some form of communication so the UNL cluster gets to know the new node's public key and a cretieria to accept it into the UNL. Please note that following is not possible:

1. UNL nodes cannot receive NPL messages from non-UNL nodes.
2. UNL nodes cannot receive consensus proposals from non-UNL nodes.

With the above restrictions in place, one way to inform the UNL about the new node is via a user input. The user input can be submitted by an external client application or from the new node itself. For simplicity, let's add a code snippet to our contract code to blindly follow the instructions given by a user input and add the new node's public key into the UNL.

```javascript
...
for (const user of ctx.users.list()) {
  for (const input of user.inputs) {
    // We blindly assume any user input is a public key
    // and add it to the UNL without any governance checks.
    const buffer = await ctx.users.read(input);
    const newNodePublicKey = buffer.toString();

    const config = await ctx.getConfig();
    config.unl.push(newNodePublicKey);
    await ctx.updateConfig(config);
    console.log("Added new public key to UNL.");
  }
}
...
```

Run `npm start` to update the cluster with new contract code. Exit the node 1 logs and view the node 4 logs with `hpdevkit logs 4`. You should keep seeing the following logs from node 4:

```
These are my cli args [ 'red', 'green', 'blue' ]
I'm NOT in the UNL. My public key: edecee17cf622edfe35a6850c462ed86bde9952ecf2179d244f2fb1806b77903aa
```

## Adding the new node to UNL

Let's create a client application to issue the UNL acceptance request using `hpdevkit gen nodejs blank-client colorsclient`.

Use the following code in `colorsclient.js`

```javascript
const HotPocket = require("hotpocket-js-client");

async function clientApp() {
  const userKeyPair = await HotPocket.generateKeys();
  const client = await HotPocket.createClient(
    ["wss://localhost:8081"],
    userKeyPair
  );

  // Establish HotPocket connection.
  if (!(await client.connect())) {
    console.log("Connection failed.");
    return;
  }

  console.log("HotPocket Connected.");

  console.log("Submitting UNL request.");
  await client.submitContractInput("<your node 4 public key>");
}

clientApp();
```

Please replace `<your node 4 public key>` with the public key mentioned in your node 4 log output.

Keep watching the node 4 log output. In the "colorsclient" directory, run `npm i` and then `node colorsclient.js`. You should see the following output:

```
Connecting to wss://localhost:8081
Connected to wss://localhost:8081
HotPocket Connected.
Submitting UNL request.
```

Meanwhile in the node 4 log output, you should see following:

```
20230527 00:59:40.644 [inf][hpc] ****Ledger created**** (lcl:55-eee7c852 state:3c90183b patch:35968a27)
These are my cli args [ 'red', 'green', 'blue' ]
I'm NOT in the UNL. My public key: edecee17cf622edfe35a6850c462ed86bde9952ecf2179d244f2fb1806b77903aa
Added new public key to UNL.
20230527 00:59:41.645 [inf][hpc] ****Ledger created**** (lcl:56-60eef9b4 state:3c90183b patch:c3d8e20f)
20230527 00:59:41.647 [inf][hpc] Contract config updated from patch file.
These are my cli args [ 'red', 'green', 'blue' ]
I'm in the UNL.
20230527 00:59:42.645 [inf][hpc] ****Ledger created**** (lcl:57-cd6f112c state:3c90183b patch:c3d8e20f)
These are my cli args [ 'red', 'green', 'blue' ]
I'm in the UNL.
```

Here you can see upon receiving the user input, the cluster added the new public key to the UNL. `[inf][hpc] Contract config updated from patch file.` denotes when HotPocket recognized the UNL update from the **configuration patch file**, which is used to subject the contract configuration to consensus. In subsequent executions of the contract in node 4, it is recognizing itself as being in the UNL.

_**NOTE**: If you cluster is small enough, adding a wrong public key to the UNL can halt the forward progress of the cluster irrecoverably, since it will no longer be able to reach majotiry (usually 80%) UNL agreement._

## Mesh network

In the above example, `hpdevkit` creates the new node and configures it so that it knows about network address/port details and public keys of all existing UNL nodes. However, in the real-world, it may be the case that the new node was created with the knowledge of only one existing nodes ip/port details. Still, eventually the new HotPocket node would discover the rest of the nodes on the network and form connections to them too. This would also make the existing nodes to add the new node's network details to their own "known peers" too. This is achieved by HotPocket's peer discovery mechanism and this happens irrespective of whether a particular node belongs to the UNL or not.
