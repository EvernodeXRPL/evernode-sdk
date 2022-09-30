# HotPocket references - Contract protocol
A [smart contract](concepts.md#smart-contract) is spawned as a child process by HotPocket for each [consensus](concepts.md#consensus) round. The communication between a smart contract and HotPocket happens as an inter-process communication using [file descriptors](https://en.wikipedia.org/wiki/File_descriptor).

There are several file descriptors that are used for different channels. Following are the file descriptors that are currently defined. Each and every message protocol will be described later.
- [User input](concepts.md#user-inputs)/[output](concepts.md#user-outputs) file descriptor
- [NPL](#npl-node-party-line-messages) file descriptor
- [Control](#control-messages) file descriptor

## User inputs
When a contract is executing, the collected [user inputs](concepts.md#user-inputs) by HotPocket are passed to the [smart contract](concepts.md#smart-contract) via the user input file descriptor. For the [user output](concepts.md#user-outputs) file descriptors, the smart contract receives a map of users with their file descriptors. The contract outputs are passed via each user's output file descriptor from the smart contract.<br><br> The user protocol are described in the [User Protocol](reference-client-protocol.md) section.

## NPL (Node Party Line) messages
The NPL channel is used to interchange messages between [smart contracts](concepts.md#smart-contract) running on each and every node in the [cluster](concepts.md#hotpocket-cluster), where HotPocket acts as an intermediary. The smart contract sends NPL messages to HotPocket via the NPL file descriptor. Then HotPocket broadcasts the message to all the connected [UNL](concepts.md#unl---unique-node-list) nodes in the cluster. Furthermore, the NPL messages sent by other peers are also sent to the smart contract from HotPocket using this file descriptor.<br><br> The NPL message can be in any format that needs to be transferred between peers. This channel can be used if we cannot assure that all the smart contracts in the cluster nodes are generating the same particular result (e.g.- random number). Hence, using this channel we can share the result between all the nodes and elect one result upon agreement.

## Control messages
Control messages are passed between [smart contracts](concepts.md#smart-contract) and HotPocket via the control file descriptor. Smart contracts can send predefined instructions to HotPocket using control messages.

### Terminating the contract
Smart contracts can terminate execution by sending the 'contract_end' control message to the HotPocket node.

    {
        'type': 'contract_end'
    }


### Changing peers
Smart contracts can add or remove peers from the HotPocket node by sending the 'peer_changeset' control message. After receiving this HotPocket will add the peer in the `add` section to the peer list, and remove the peer in `remove` section from the peer list.

    {
        'type': 'peer_changeset',
        'add': ['<ip1>:<port1>', '<ip2>:<port2>', ...],
        'remove': ['<ip1>:<port1>', '<ip2>:<port2>', ...]
    }