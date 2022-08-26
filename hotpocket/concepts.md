# HotPocket concepts

_This article assumes you already have a basic understanding of decentralized applications in general._

HotPocket is a smart contract consensus engine. It is capable of making an application written in traditional programming methologies a decentralized one. It does that by facilitating the decentralized communication infrastructure while leaving the application developer to be only concerned with the application logic at a "single-instance" point of view.

## HotPocket cluster

HotPocket cluister is a collection of machines, each running an instance (a.k.a node) of HotPocket software, that are configured to communicate with each other. Each HotPocket node takes care of observing the application activities that take place on that machine and communicating about that with other HotPocket nodes in the cluster. Via a predetermined algorithm, they contrast and compare the observations of all HotPocket nodes in the cluster and arrive at a final conclusion. This process is called "consensus" and it is an integral part of making applications decentralized.

## Consensus

Each HotPocket node collects specific kinds of information to be subjected to consensus. That is, those pieces of information are used to compare and contrast itself with same types of information heard from other nodes in the cluster. Primarily, HotPocket subjects following information for consensus: [users](#users), [user inputs](#user-inputs), [user outputs](#user-outputs) and [state](#state). The information is checked to verify whether most of the nodes are in "agreement" with each other about observed information. After checking which node is agreeing with what, each node independently arrives at a conclusion of what would be considered as the final view of the cluster. This decision is made according to a predetermined "consensus" algorithm. This process happens according to a pre-configured time schedule which is called a consensus "round".

Ideally, at every conensus round, a HotPocket node should be able to arrive at a fair conclusion that has the majority agreement of other nodes in the cluster. In such a case, HotPocket creates a "ledger" containing the final conclusion it arrived at. It then invokes the [smart contract](#smart-contract) application to act on the information which was subjected to consensus.

If HotPocket could not reach a consensus agreement at the end of a round, it simply gives up and starts the next round without creating a ledger or invoking the smart contract.

## Smart contract

A HotPocket smart contract is an application software of which a copy exists alongside each HotPocket node. The HotPocket node invokes the smart contract as it deems necessary. For example, HotPocket may invoke the smart contract at the end of a consensus round. The smart contract is capable of interpreting and acting upon the consensed information passed to it by HotPocket.

## Users

External parties which are called "Users" can connect to HotPocket nodes using websockets. Each HotPocket node keeps track of users connected to that node. All nodes share the connected user information with each other which is then used to form a picture of all users connected to all the nodes. In essance, if a user connects to a HotPocket node, conceptually, it is as if the user is connected to the cluster. Practically, the node that the user connects to, acts as the user's doorway to the cluster.

### User identification

HotPocket uniquely identifies a user by the ed22519 public key presented by the user upon establishing a websocket connection to HotPocket. This is cryptographically verified by HotPocket and it is assumed that all subsequent data exchanged via the established connection belongs to the user represented by that public key.

## User inputs

User can send arbitary binary data to the HotPocket node, to be interpreted by the smart contract application associated with that HotPocket node. Each HotPocket node shares all user inputs it received with the other nodes on the cluster. After the consensus process, the consensed inputs are fed into the smart contract. The smart contract can interpret the user inputs as it deems necessary and act upon it.

## User outputs

## State