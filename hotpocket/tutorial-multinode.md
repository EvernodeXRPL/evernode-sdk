# HotPocket tutorial - multiple nodes

This tutorial is a continuation of [HotPocket basics tutorial](tutorial-basics.md).

So far we have interacted with a single HotPocket node. HotPocket developer kit by default creates a cluster with 3 nodes. We can interact with different nodes at different times to better mimic real world use cases.

## Inspecting consensus behaviour

1. Run `npm start` to ensure that your smart contract is deployed and the HotPocket cluster is running. You should see log output from node 1 of the cluster.
2. On two other terminals, run the commands `hpdevkit logs 2` and `hpdevkit logs 3`. Now you should see similar-looking log output from all three HotPocket nodes.
3. To simulate one of the nodes becoming offline, on another terminal, run the command `hpdevkit stop 3`. This will cause the node number 3 to stop and other two nodes to go into a waiting mode with following log output.

```
20220829 13:29:41.190 [inf][hpc] Not enough peers proposing to perform consensus. votes:2 needed:3
20220829 13:29:42.189 [inf][hpc] Not enough peers proposing to perform consensus. votes:2 needed:3
20220829 13:29:43.190 [inf][hpc] Not enough peers proposing to perform consensus. votes:2 needed:3
```

This is indicating that to work as a cluster and perform consensus verification, each HotPocket node needs 3 votes in total, but they are only seeing 2 votes. Because 2 out of 3 falls below the default **consensus threshold** of 80%, the active nodes simply give up on consensus.

If you start back the node 3 with `hpdevkit start 3`, you should see that the entire cluster gets back into normal operation with 'Ledger created' logs before long.

```
20220829 13:33:07.189 [inf][hpc] Not enough peers proposing to perform consensus. votes:2 needed:3
20220829 13:33:08.190 [inf][hpc] Not enough peers proposing to perform consensus. votes:2 needed:3
20220829 13:33:08.913 [inf][hpc] No longer weakly connected.
20220829 13:33:09.944 [inf][hpc] ****Ledger created**** (lcl:258-43f73e0b state:7f8e4a33 patch:4e7758a5)
20220829 13:33:10.943 [inf][hpc] ****Ledger created**** (lcl:259-2fbc18e1 state:7f8e4a33 patch:4e7758a5)
```

## Experiment with a larger cluster

The cluster we are currently working on consists of 3 nodes. This is the default cluster size used by HotPocket developer kit. Let's increase the cluster size to 5.

1. Run `hpdevkit clean`. This will purge all nodes of the existing cluster. It is required to do this before resizing the cluster.
2. Set the cluster size environment variable according to your operating system.

   ```
   # Windows (command prompt)
   set HP_CLUSTER_SIZE=5

   # Windows (powershell)
   $env:HP_CLUSTER_SIZE=5

   # Linux (bash)
   export HP_CLUSTER_SIZE=5
   ```

3. Run `npm start`. This will deploy the smart contract to a new cluster with 5 nodes and display the log output.
4. On 4 other terminals, run the command `hpdevkit logs 2` and so on to view the log output of the remaining 4 nodes. You should see they are all running in similar fashion to each other.
5. On another terminal, stop one of the node number 5 with `hpdevkit stop 5`.

Now, even with node 5 being stopped, you should see that other nodes are still operating normally with 'Ledger created' logs. This is due to the fact that, even with one of the nodes offline, 4 out of 5 nodes are still active. Since this fulfills the default consensus threshold of 80%, the other nodes can continue performing consensus.

## Node recovery

Because the 5th node is currently stopped, when its start up next time, it needs to get in-sync with the rest of the cluster and catch-up with the ledgers that it missed. The recovering node does this automatically by requesting the missing information from other stable nodes in the cluster.

Start the stopped node with `hpdevkit start 5` and then immediately run `hpdevkit logs 5` to witness its catchup behaviour with a log like the one below.

```
20220829 13:51:41.921 [inf][hpc] We are not on the consensus ledger, we must request history from a peer.
20220829 13:51:41.961 [inf][hpc] Hpfs ldgr sync: Target added. Hash:2436a88d /primary/0
20220829 13:51:42.045 [inf][hpc] Hpfs ldgr sync: Achieved target:2436a88d /primary/0
20220829 13:51:43.676 [inf][hpc] ****Ledger created**** (lcl:91-09b59a37 state:7f8e4a33 patch:d75c5631)
20220829 13:51:44.676 [inf][hpc] ****Ledger created**** (lcl:92-1e676e7f state:7f8e4a33 patch:d75c5631)
```

With the 5th node caught up with the rest of the cluster, the cluster can now operate with 5 active nodes. This means it now has the capacity to tolerate any one of the nodes failing without any disruption to the continuity.

## Connecting to different nodes

HotPocket developer kit assigns different port numbers for the nodes it creates in the cluster. We can use these port numbers to decide which node to connect to during client application development and testing. HotPocket developer kit by default assigns port numbers 8081 onwards sequentially for each node in the cluster.

Let's modify our client application to specify port number as follows.

```javascript
const port = process.argv[2];
const client = await HotPocket.createClient(
  ["wss://localhost:" + port],
  userKeyPair
);
```

Connect the client to node 1 with `node myclient.js 8081`. You should see similar output as below.

```
Connecting to wss://localhost:8081
Connected to wss://localhost:8081
HotPocket Connected.
Saying hello...
```

Similarly you can connect to node 2 with `node myclient.js 8082` and so on.

Next: [Persisting data](tutorial-persistdata.md)