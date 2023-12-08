# HotPocket tutorial - NPL (Node Party Line) messaging

While executing smart contracts, information may sometimes need to be exchanged within the UNL (Unique Node List).

For example, during smart contract execution, you may have a requirement to create a random number for a database insert. However, this cannot be achieved within a single node, and if such an event occurs, it would lead to a break of consensus, since each node writes a different value to the database.

See the code sample given below. Here, each node generates a random number and saves it to a file called `data.txt` in its state:

```javascript
const HotPocket = require("hotpocket-nodejs-contract");
const fs = require("fs").promises;

const myContract = async (ctx) => {
  const min = 1;
  const max = 5;

  const random = Math.floor(Math.random() * (max - min + 1)) + min;
  const filename = "data.txt";
  await fs.appendFile(filename, random + "\n");
};

const hpc = new HotPocket.Contract();
hpc.init(myContract);
```

The following output would then be generated:

```
20220919 03:38:06.279 [inf][hpc] ****Ledger created**** (lcl:1-357f3289 state:9ae9215d patch:e1bb67c7)
20220919 03:38:06.529 [inf][hpc] Hpfs cont sync: Target added. Hash:cd9e86ee /state
20220919 03:38:08.863 [inf][hpc] Hpfs cont sync: Achieved target:cd9e86ee /state
20220919 03:38:09.013 [inf][hpc] Not enough peers proposing to perform consensus. votes:0 needed:3
20220919 03:38:09.763 [inf][hpc] Not enough peers proposing to perform consensus. votes:2 needed:3
20220919 03:38:11.268 [inf][hpc] ****Ledger created**** (lcl:2-9fb66bf3 state:cd9e86ee patch:e1bb67c7)
20220919 03:38:11.529 [inf][hpc] Hpfs cont sync: Target added. Hash:373fe9ef /state
20220919 03:38:11.652 [inf][hpc] Hpfs cont sync: Achieved target:373fe9ef /state
20220919 03:38:11.763 [inf][hpc] Not enough peers proposing to perform consensus. votes:1 needed:3
```

This actually causes the entire cluster to go out of sync, and consensus will break.

To avoid this problem, [NPL messaging](reference-contract-protocol.md#npl-node-party-line-messages) can be used. Using NPL messaging, nodes can come to a collective decision and break of consensus can be avoided.

Let's try to get a basic understanding of how NPL can be used.

## Sending messages within the UNL of the HotPocket cluster

If a particular node is able to listen to at least a single message from the others nodes in the UNL, it will log the message.

[HotPocket config](reference-configuration.md) specifies `npl` mode as `private` by default, so NPL messages can only be passed between UNL nodes. Setting `npl` to `public` will enable any connected node (non-UNL) to receive NPL messages. However, HotPocket will reject NPL messages that are sent by non-UNL nodes.

**_NOTE :_** _NPL messaging is not available in read-only contract mode, and therefore this should be done in a consensus invocation ( !ctx.readonly )._

The following code attempts to send a message within the UNL:

```javascript
const HotPocket = require("hotpocket-nodejs-contract");

const myContract = async (ctx) => {
  if (!ctx.readonly) {
    // Start listening to incoming NPL messages before we send ours.
    const promise = new Promise((resolve, reject) => {
      ctx.unl.onMessage((node, msg) => {
        // Filter the messages received only from other nodes, since we receive our own message
        // as well.
        if (ctx.publicKey !== node.publicKey) {
          resolve(`${node.publicKey} said ${msg} to me.`);
        }
      });
    });

    await ctx.unl.send("Hello");
    const receipt = await promise;
    console.log(receipt);
  }
};

const hpc = new HotPocket.Contract();
hpc.init(myContract);
```

The following output will be generated:

```
20220916 03:53:23.360 [inf][hpc] ****Ledger created**** (lcl:1-1208b520 state:30e50b13 patch:68617629)
ed371a8134200496a3a8a2439de0b61bd25bc2000bfe94e37b70276f357fc9702d said Hello to me.
20220916 03:53:24.355 [inf][hpc] ****Ledger created**** (lcl:2-a766ffae state:30e50b13 patch:68617629)
ed66bcc2578ec0aca85868256431cdc7485afb5af5696203035e02a6aae382b7b2 said Hello to me.
20220916 03:53:25.356 [inf][hpc] ****Ledger created**** (lcl:3-77821703 state:30e50b13 patch:68617629)
```

## Collecting multiple messages until timeout

The duration of the timeout (in milliseconds) is determined with the use of [roundtime](reference-configuration.md/#contract) of the consensus:

```javascript
const HotPocket = require("hotpocket-nodejs-contract");

const myContract = async (ctx) => {
  if (!ctx.readonly) {
    // To get the HotPocket contract configuration.
    const hpconfig = await ctx.getConfig();

    // Start listening to incoming NPL messages before we send ours.
    const promise = new Promise((resolve, reject) => {
      let nodeMessages = [];
      let msg = "";

      // Wait only for half of roundtime.
      const timeoutMs = Math.ceil(hpconfig.consensus.roundtime / 2);

      let timer = setTimeout(() => {
        clearTimeout(timer);
        nodeMessages.forEach((element) => {
          msg = `${msg} \n ${element.node} said ${element.msg} to me.`;
        });
        // Output all the messages received in the timeout.
        resolve(msg);
      }, timeoutMs);

      ctx.unl.onMessage((node, msg) => {
        // Filter the messages received only from other nodes, since we receive our own message
        // as well.
        if (ctx.publicKey !== node.publicKey) {
          // Collect messages received from the nodes in UNL to an array.
          nodeMessages.push({ node: node.publicKey, msg: msg });
        }
      });
    });

    await ctx.unl.send("Hello");

    const receipt = await promise;
    console.log(receipt);
  }
};

const hpc = new HotPocket.Contract();
hpc.init(myContract);
```

The following output will be generated:

```
20220916 06:17:38.241 [inf][hpc] ****Ledger created**** (lcl:2-0d1cb434 state:1003873b patch:dd100440)

 ed65e53ae912581501521bb9323309903556d08794614a037f95013f38fce5755c said Hello to me.
 edb6382ec6e78549d1ea2b40930b2448f9dfb49a22dc13caf54d1cbd31968603a6 said Hello to me.
20220916 06:17:40.244 [inf][hpc] ****Ledger created**** (lcl:3-1b3131ab state:1003873b patch:dd100440)

 ed65e53ae912581501521bb9323309903556d08794614a037f95013f38fce5755c said Hello to me.
 edb6382ec6e78549d1ea2b40930b2448f9dfb49a22dc13caf54d1cbd31968603a6 said Hello to me.
20220916 06:17:42.243 [inf][hpc] ****Ledger created**** (lcl:4-cbc3d0bd state:1003873b patch:dd100440)
```

**_NOTE :_** _A 3-node HotPocket cluster is used in the example above._

## Advance usage of NPL

Now let's try to develop something useful using NPL messages. As mentioned before, the nodes can make collective decisions using NPL messages.

Let's go back to the example of random number generation:

- First, each node generates a random number and broadcasts it to the UNL.
- Each node then maintains an array of numbers that it has received.
- Once a node receives all messages from its peers, or the timeout period is reached, the node has to select the maximum from the received random numbers.
- The node then tries to save the maximum value in the `data.txt` file in its state.

**_NOTE :_** _A stringified JSON object is used as the message in the example below._

```javascript
const HotPocket = require("hotpocket-nodejs-contract");
const fs = require("fs").promises;

const myContract = async (ctx) => {
  const filename = "data.txt";

  if (!ctx.readonly) {
    const unlSize = ctx.unl.count();
    const hpconfig = await ctx.getConfig();
    // Wait only for half of roundtime.
    const timeoutMs = Math.ceil(hpconfig.consensus.roundtime / 2);

    let completed = false;

    // Start listening to incoming NPL messages before we send ours.
    const promise = new Promise((resolve, reject) => {
      let receivedNos = [];

      function getMax() {
        console.log(`Received Numbers :`, receivedNos);
        let max = 0;
        for (const randomNumber of receivedNos) {
          if (randomNumber > max) {
            max = randomNumber;
          }
        }
        return max;
      }

      let timer = setTimeout(() => {
        clearTimeout(timer);
        completed = true;
        // If we've received less than what we expect, throw error.
        if (receivedNos.length < unlSize)
          reject("Error generating the random number.");
        else resolve(getMax());
      }, timeoutMs);

      ctx.unl.onMessage((node, msg) => {
        if (!completed) {
          const obj = JSON.parse(msg.toString());
          if (obj.key === "randomNumber") {
            const number = Number(obj.value);
            receivedNos.push(number);
          }
          if (receivedNos.length === unlSize) {
            clearTimeout(timer);
            completed = true;
            resolve(getMax());
          }
        }
      });
    });

    const min = 1;
    const max = 5;

    const random = Math.floor(Math.random() * (max - min + 1)) + min;
    await ctx.unl.send(
      JSON.stringify({
        key: "randomNumber",
        value: random,
      })
    );

    const receipt = await promise;
    console.log("Decided Random No.:", receipt);
    await fs.appendFile(filename, receipt + "\n");
  }
};

const hpc = new HotPocket.Contract();
hpc.init(myContract);
```

The following output will be generated:

```
20220919 05:24:03.512 [inf][hpc] ****Ledger created**** (lcl:16-67a3e08e state:bd32fe65 patch:746911a7)
Received Numbers : [ 2, 5, 5 ]
Decided Random No.: 5
20220919 05:24:04.512 [inf][hpc] ****Ledger created**** (lcl:17-e215ee58 state:ec81f255 patch:746911a7)
Received Numbers : [ 4, 4, 2 ]
Decided Random No.: 4
20220919 05:24:05.512 [inf][hpc] ****Ledger created**** (lcl:18-e30b33b5 state:7cbdc0a2 patch:746911a7)
Received Numbers : [ 2, 1, 2 ]
Decided Random No.: 2
```

**_NOTE :_** _A 3-node HotPocket cluster is used in the example above, and there is no break of consensus due to a state mismatch._

**_To implement a more advanced decision-making logic, you can extend this implementation to voting and selecting a majority from a list of suggestions._**

Next: [Instance sync](tutorial-instance-sync.md)
