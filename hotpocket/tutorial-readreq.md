# HotPocket tutorial - read requests

Read requests are a user input/output mechanism that operates on a single HotPocket node. They do not have the overhead of operating across the entire cluster for consensus verification. In read requests, user inputs are processed only by the node that the input was sent to, and the output is sent back to the user immediately. The benefit is the lesser wait time compared to consensus-based input/output at the expense of cluster-wide consistency. Because read requests are not subjected to consensus, the smart contract is not allowed to perform any writes to [state](concepts.md/#state) during processing of read requests.

## Smart contract readonly mode

Let's modify our smart contract with following `if..else` block to support read requests.

```javascript
if (ctx.readonly) {
  // Code that only executes in readonly invocations of the contract.
  // In this mode, we do not have write access to the filesystem.
} else {
  // Save the input to a user-specific file name.
  const filename = `${user.publicKey}.log`;
  await fs.appendFile(filename, message + "\n");

  // Get total no. of messages sent by user so far.
  const allMessages = (await fs.readFile(filename)).toString();
  const total = allMessages.split("\n").length - 1;

  user.send(`You said '${message}'`);

  // Include total message count in the reply.
  user.send(`Thanks for talking to me ${total} times`);
}
```

Because any processing done by the smart contract for read requests are not subjected to consensus, HotPocket invokes the smart contract in **readonly** mode and indicates that fact with the `ctx.readonly` field. We have moved the previous user-input-processing code we had, into the 'non readonly' code portion.

Let's serve some useful data for a read request.

```javascript
if (ctx.readonly) {
  if (message === "count") {
    // Reply the user with the no. of .log files.
    const files = await fs.readdir(".");
    const logFileCount = files.filter((f) => f.endsWith(".log")).length;
    user.send(logFileCount.toString());
  }
}
```

In the above code, if the smart contract receives an input with the string 'count' in readonly mode, it will reply with the total number of log files it has persisted so far.

## Submit read request

Now, let's modify our client application to submit a read request.

```javascript
console.log("HotPocket Connected.");

console.log("Submitting read request...");
const count = await client.submitContractReadRequest("count");
console.log("User file count:", count);
```

Read requests are "synchronous", which means that the smart contract is expected to immediately respond for the reuqest. Furthermore, unlike consensus user-inputs and user-outputs which did not have any association between them, read requests and replies are tightly coupled. Every user-input submitted as a read request is expected to produce exactly one user-output.

After making above code modifications, let's deploy the smart contract (`npm start`) and run the client application (`node myclient.js`). You will see following log in the client application.

```
HotPocket Connected.
Submitting read request...
User file count: 0
Saying hello...
Received outputs:
You said 'hello'
Thanks for talking to me 1 times
```

Run the client application again. This time you will see the file count has been increased by 1 due to the previous 'hello' input we submitted.

```
Submitting read request...
User file count: 1
```

## Response delay - conesnsus vs read requests

Using above example, you should be able to observe the difference in delay between inputs and outputs when using read requests as opoosed to consensus. The time different between `Submitting read request...` and `User file count: 1` should be shorter than the time difference between `Saying hello...` and `Received outputs:`. You can benefit from this to provide a faster response time to users depending on the needs of your application.

Read requests are a way of submitting user inputs and receiving outputs while bypassing consensus. It's a great way to retrieve information from a HotPocket node but the information consistency is not guaranteed as we are bypassing consensus.