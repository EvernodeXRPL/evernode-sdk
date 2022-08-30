# HotPocket tutorial - persisting data

This tutorial is a continuation of [Working with multiple nodes](tutorial-multinode.md).

Because HotPocket smart contracts are regular Linux applications, they have direct access to on-device file storage. Therefore, you can use regular filesystem operations to save data onto disk and access them between subsequent executions of the smart contract.

When HotPocket invokes your smart contract, it sets the "current working directory" of the smart contract application to be the "state" directory monitored by HotPocket. After the smart contract exits, HotPocket subjects the data that was written under that directory to consensus so the HotPocket cluster [state](concepts.md#state) is in-sync. Therefore, any files/databases that you maintain under the "current working directory" of the smart contract will be subjected to consensus.

_Your smart contract can even make web/API calls to upload data or anything else that a regular application can do. However, those activities will not be subjected to consensus._

## Save user inputs into state

From [HotPocket basics tutorial](tutorial-basics.md), your smart contract code looks like this.

```javascript
const HotPocket = require("hotpocket-nodejs-contract");

const mycontract = async (ctx) => {
  // Your smart contract logic.
  for (const user of ctx.users.list()) {
    console.log("User public key", user.publicKey);

    // Loop through inputs sent by each user.
    for (const input of user.inputs) {
      const buffer = await ctx.users.read(input);

      const message = buffer.toString();
      console.log("Received input:", message);
      user.send(`You said '${message}'`);
      user.send(`Thanks for talking to me!`);
    }
  }
};

const hpc = new HotPocket.Contract();
hpc.init(mycontract);
```

Let's update the contract to save user messages to a file and include total no. of messages in the reply.

```javascript
const HotPocket = require("hotpocket-nodejs-contract");
const fs = require("fs").promises;
```

```javascript
const message = buffer.toString();
console.log("Received input:", message);

// Save the input to a user-specific file name.
const filename = `${user.publicKey}.log`;
await fs.appendFile(filename, message + "\n");

// Get total no. of messages sent by user so far.
const allMessages = await fs.readFile(filename);
const total = allMessages.split("\n").length;

user.send(`You said '${message}'`);

// Include total message count in the  reply.
user.send(`Thanks for talking to me ${total} times`);
```

As you can see we are using standard NodeJs filesystem API to read/write data on disk. The data that gets saved into the files are subjected to consensus by HotPocket after the smart contract exits. This means any data that gets saved under the current working directory of the smart contract will be equal among all the HotPocket nodes in the cluster.