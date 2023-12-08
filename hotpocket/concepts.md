# HotPocket concepts

_This article assumes you already have a basic understanding of decentralized applications in general._

HotPocket is a smart contract consensus engine. It is capable of converting an application written in traditional programming methodologies into a decentralized application. It does so by facilitating the decentralized communication infrastructure, which leaves the application developer free to manage the application logic at a "single-instance" point of view.

## HotPocket cluster

HotPocket cluster is a collection of machines, each running an instance (i.e. node) of HotPocket software, that are configured to communicate with and trust each other. Each HotPocket node observes the application activities that take place on the machine and communicates it to the other HotPocket nodes in the cluster. Using a predetermined algorithm, they contrast and compare the observations of all HotPocket nodes in the cluster and arrive at a final conclusion. This process is called [consensus](#consensus) and it is an integral part of making applications decentralized.

## Smart contract

A HotPocket smart contract is an application software of which a copy exists on each HotPocket node. The HotPocket node invokes the smart contract as it deems necessary. For example, HotPocket may invoke the smart contract at the end of a [consensus](#consensus) round. The smart contract is capable of interpreting and acting upon the consensed information passed to it by HotPocket. During execution, the smart contract is capable of exchanging information with the HotPocket node via an established communication channel between itself and HotPocket. As far as HotPocket is concerned, a smart contract's primary job is to process [user inputs](#user-inputs), modify [state](#state), and produce [user outputs](#user-outputs).

## Consensus

Each HotPocket node collects specific kinds of information to be subjected to consensus. These pieces of information are used to compare and contrast itself with same types of information heard from other nodes in the cluster. Primarily, HotPocket subjects the following information for consensus: [users](#users), [user inputs](#user-inputs), [user outputs](#user-outputs) and [state](#state). Using a fingerprinting mechanism (hashing) for efficiency, the information is checked to verify whether most of the nodes are in "agreement" with each other about observed information. After checking which node is agreeing with what, each node independently arrives at a conclusion of what would be considered as the final view of the cluster. This decision is made according to a predetermined "consensus" algorithm. This process happens according to a pre-configured time schedule (usually every few seconds) which is called a consensus "round".

Ideally, at every consensus round, a HotPocket node should be able to arrive at a fair conclusion that has the majority agreement of other nodes in the cluster. In such a case, HotPocket creates a "ledger" containing the final conclusion it arrives at. It then invokes the [smart contract](#smart-contract) application to act on the information which was subjected to consensus.

If HotPocket could not reach a consensus agreement at the end of a round, it simply gives up and starts the next round without creating a ledger or invoking the smart contract.

### UNL - Unique Node List

Each HotPocket node maintains a list of other nodes that it trusts. This is called the Unique Node List (UNL) and it is required for [consensus](#consensus), where the process is carried out only among trusted nodes on this list. A trusted node is identified by its ed22519 public key which is presented and cryptographically verified at the time of establishment of a connection between the two nodes. If the public key of a connected node is among the UNL, HotPocket chooses to consider the information presented by the other node in its consensus checks.

### Users

External parties called "Users" can connect to HotPocket nodes using WebSockets. Each HotPocket node keeps track of users connected to that node. All nodes share the connected user information with each other, which is then used to form a picture of all users connected to all the nodes. In essence, if a user connects to a HotPocket node, conceptually, it is as if the user is connected to the cluster. Practically, the node that the user connects to acts as the user's doorway to the cluster.

HotPocket uniquely identifies a user by the ed22519 public key presented by the user upon establishing a WebSocket connection with HotPocket. This is cryptographically verified by HotPocket and it is assumed that all subsequent data exchanged via this established connection belongs to the user represented by that public key.

### User inputs

Users can send arbitrary binary data to HotPocket nodes, to be interpreted by the smart contract application associated with each HotPocket node. Each HotPocket node shares all user inputs it receives with the other nodes in the cluster. After the consensus process is finished, every node feeds the consensed inputs into the smart contract. This ensures the copy of the smart contract on each HotPocket node gets fed the same set of user inputs as the other nodes. The smart contract can interpret the user inputs as it deems necessary and act upon this information.

### User outputs

The HotPocket smart contract application, based on its logic, can generate arbitrary binary data to be sent to specific users (i.e. user outputs) connected to the cluster. Each HotPocket node captures the user outputs generated by the smart contract and subjects them to consensus. Consensus helps ensure that a majority of HotPocket nodes agree on the generated set of user outputs. The consensed outputs are then sent back to their intended recipient users via the HotPocket node that each user is connected to.

### State

HotPocket smart contracts can persist data on to the disk via regular file system operations, hence it is preserved and accessible between different invocations of the contract. HotPocket subjects the persisted data to consensus so that majority of nodes are in agreement on the data that gets persisted on to their disks. The consensed data on disk is called the smart contract "state" of the entire HotPocket cluster. When the smart contract is invoked after a consensus round, it has read/write access to the consensed data on disk (state) via regular file system operations.

If a HotPocket node finds out that its state is "out of sync" with other nodes on the cluster, it will attempt to synchronize its state with the consensed state of the cluster. In this case it will not invoke the smart contract until its state is in sync with the cluster.

_**Smart contract in "state":** Although not required to do so, it is desirable for the smart contract application software itself to reside on "state". This ensures the application files of the smart contract are also subjected to consensus, guaranteeing that each HotPocket node is hosting the same smart contract version/logic._

### LCL - Last Closed Ledger

At the end of each consensus round (once consensus is reached and before [smart contract](#smart-contract) execution is started), HotPocket creates a ledger. Every ledger has a hash which includes the previous ledger hash and the current data hash. Together, this builds the chain where every ledger hash is based on the previous ledger hash. Furthermore, the ledger includes the state hash, user hash, user input/output hashes, etc. Every ledger has a sequence number which increments upon ledger creation.

### Consensus round

The following depicts a simplified overview of what happens during an arbitrary consensus round N.

1. The job of consensus round N is to finalize the "consensed" data based on "local" data. Local data are the things that were captured on a single HotPocket node.
2. The local data for round N consists of:
   - New user inputs recently submitted by users to this node.
   - User outputs generated by contract execution from round N-1.
   - Filesystem state hash from round N-1.
3. Consensus round N will then circulate the above data between UNL nodes and arrive at a consensus conclusion. Nearing the end of round N, we have "consensed" data.
4. At this point, HotPocket creates a ledger which contains consensed data hashes. So it includes:
   - Hashes of consensed user inputs for round N
   - Hashes of consensed user outputs from round N-1
   - Filesystem state hash from round N-1
5. After ledger creation, HotPocket communicates to users.
   - For consensed user inputs, we send back a status update to relevant users that their inputs made it to the ledger.
   - Consensed user outputs from round N-1 will be sent to relevant users.
6. Then the smart contract is executed.
   - Filesystem represents the consensed state that's in the last closed ledger (the state from round N-1).
   - User inputs that are fed into the contract are the ones that are in the lase closed ledger (user inputs from round N).
7. After contract exits, the generated "local" user outputs collected for the use of round N+1. Then we move on to round N+1 and repeat the process.

## Read requests

In previous sections, it was explained how HotPocket nodes work in unison and how the smart contract is called upon to act on the consensed information. This ensures that the entire HotPocket cluster moves forward in-sync with each other and each smart contract acts upon the consensed information. Even though this is desirable for security, it has a time penalty resulting from having to carry out consensus verification among the entire cluster of nodes.

"Read requests" are a great way to communicate with a smart contract on a single HotPocket node without the overhead of consensus. It constitutes a request/response cycle in which the smart contract provides the data that the user requested. To facilitate this, upon receiving a read request from the user, HotPocket invokes the smart contract in **read-only** mode. In this mode, the smart contract has read-only access to the last-consensed [state](#state) and extracts the required information out of it according to the request it received as a "user input". It can then send the extracted information to the user in the form of a "user output".

Next: [HotPocket basics tutorial](tutorial-basics.md)
