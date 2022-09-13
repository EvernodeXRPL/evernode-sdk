# HotPocket tutorial - Contract protocols
[Smart contract](concepts.md#smart-contract) is spawn as a child process by HotPocket on each [consensus](concepts.md#consensus) round. The communication between smart contract and HotPocket happens as inter process communication using [file descriptors](https://en.wikipedia.org/wiki/File_descriptor).

There are several file descriptors that are being used for different channels. Following are the file descriptors that are defined. Each and every message protocols will be described later.
- [User input](concepts.md#user-inputs)/[output](concepts.md#user-outputs) file descriptors
- [NPL](#npl-node-party-line-messages) file descriptor
- [Control](#control-messages) file descriptor

## User inputs
When contract is executing, collected [user inputs](concepts.md#user-inputs) by HotPocket are parsed to [smart contract](concepts.md#smart-contract) via user input file descriptor. For the [user output](concepts.md#user-outputs) file descriptors smart contract receives a map of users with their file descriptors. The contract outputs are parsed via each user's output file descriptor from the smart contract.<br><br> The user protocols are described in [User Protocols](tutorial-user-protocols.md) section.

## NPL (Node Party Line) messages
NPL channel is used to interchange messages between [smart contracts](concepts.md#smart-contract) running on each and every node in the [cluster](concepts.md#hotpocket-cluster). Here also HotPocket acts are a middleman. Smart contract sends NPL messages to HotPocket via the NPL file descriptor. Then HotPocket broadcasts the message to all the connected [UNL](concepts.md#unl---unique-node-list) nodes in the cluster. And furthermore the NPL messages sent by other peers are also sent to the amart contract from HotPocket using this file descriptor.<br><br> The NPL message can be an any format of message that needs to bee transferred between peers. This channel can be used if we cannot assure that all the smart contracts in the cluster nodes are generating same particular result (Ex: Random number), So using this channel we can share the result between all the nodes and elect one result upon an agreement.

## Control messages
Control messages are passed between [smart contract](concepts.md#smart-contract) and HotPocket via control file descriptor. Smart contract can send predefined instructions to HotPocket using control messages.

### Terminating the contract
Smart contract can terminate the execution by sending the contract_end control message to the HotPocket.

    {
        'type': 'contract_end'
    }


### Changing peers
Smart contract can add or remove peers from the HotPocket node by sending the peer_changeset control message. After receiving this HotPocket will add peer in `add` section to the peer list and remove the peer in `remove` section from the peer list.

    {
        'type': 'peer_changeset',
        'add': ['<ip1>:<port1>', '<ip2>:<port2>', ...],
        'remove': ['<ip1>:<port1>', '<ip2>:<port2>', ...]
    }

Next: [HotPocket basics tutorial](tutorial-basics.md)