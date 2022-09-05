# HotPocket tutorial - persisting data

This tutorial is a continuation of [working with multiple nodes](tutorial-multinode.md).

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
const allMessages = (await fs.readFile(filename)).toString();
const total = allMessages.split("\n").length - 1;

user.send(`You said '${message}'`);

// Include total message count in the reply.
user.send(`Thanks for talking to me ${total} times`);
```

As you can see we are using standard NodeJs filesystem API to read/write data on disk. The data that gets saved into the files are subjected to consensus by HotPocket after the smart contract exits. This means any data that gets saved under the current working directory of the smart contract will be equal among all the HotPocket nodes in the cluster.

Run `npm start` and verify the smart contract is deployed and running. If you have followed the previous tutorial, it should be running on 5 HotPocket nodes.

## Access saved data as a user

Let's see what the client application sees now, when it submits a user input into the smart contract.

Connect the client application to node 1 with `node myclient.js 8081`. You should see following output from the smart contract.

```
You said 'hello'
Thanks for talking to me 1 times
```

Exit and run the client application again. This time you should see following output.

```
You said 'hello'
Thanks for talking to me 2 times
```

Now, connect the client application to node 3 with `node myclient.js 8083`. The output will be

```
You said 'hello'
Thanks for talking to me 3 times
```

As you can see, regardless of the node you connect to, data maintained by the smart contract is preserved across all nodes consistently.

If you recall from the following code in the smart contract, the user messages are saved into a file named after the user public key. This means the message count should keep increasing as long as the client application connects using the same key pair.

```javascript
// Save the input to a user-specific file name.
const filename = `${user.publicKey}.log`;
await fs.appendFile(filename, message + "\n");
```

Now, let's connect as a different (new) user. Move or rename the 'user.key' file in 'myclient' directory into a different location. Then run `node myclient.js 8083`. This will cause the client application to generate a new key pair and connect to node 3. You will see following output.

```
You said 'hello'
Thanks for talking to me 1 times
```

Now, restore the previous 'user.key' file you moved/renamed and run the client application again.

```
You said 'hello'
Thanks for talking to me 4 times
```

As you can see, our HotPocket smart contract keeps users' input data independently of each other and the respective users can access their information using the correct key pair. This is the basis of maintaining 'user accounts' in HotPocket smart contracts.

_In above examples, we are using simple text files to maintain saved data. But you can use any local database provider like SQLite according to your preference. As long as the files are maintained within the current working directory of the app, HotPocket will keep them in-sync across the cluster according to its consensus mechanism._
