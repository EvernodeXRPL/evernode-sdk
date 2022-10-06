# HotPocket references - Contract protocol

HotPocket defines the smart contract as a regular POSIX application that can receive Inputs, produce Outputs and also persist State to permanent storage. You can use any POSIX-compliant programming platform to create HotPocket smart contracts. If you are interested, you can also develop a library that helps to execute the HotPocket features efficiently.

Currently, there are two such libraries available for [NodeJS](https://github.com/HotPocketDev/hp-nodejs-contract) and [C](https://github.com/HotPocketDev/hp-c-contract).

The HotPocket [smart contract](concepts.md#smart-contract) is spawned as a child process by HotPocket for each [consensus](concepts.md#consensus) round. The communication between the smart contract and HotPocket consensus engine happens as an inter-process communication using [file descriptors](https://en.wikipedia.org/wiki/File_descriptor). As per the IBM definition, the file descriptor is an unsigned integer used by a process to identify an open file. In HotPocket there is a set of application-specific file descriptors.These file descriptors are used for different channels. Following are the file descriptors that are currently defined. Each and every message protocol will be described later.
- User input/output file descriptors
- NPL file descriptor
- Control file descriptor

When implementing a smart contract on top of HotPocket consensus engine, you need consider following basic components of that HotPocket smart contract.


  - [Contract execution context](#contract-execution-context)
  - [Control channel](#control-channel)
  - [Users/Clients channel](#usersclients-channel)
  - [NPL (Node Party Line) channel](#npl-node-party-line-channel)

Let's try to get a basic understanding of what are these components.

## Contract execution context

The HotPocket consensus engine passes context parameters in `JSON` format as a command line arguments to the contract when it's being executed. (The engine actually writes the contract arguments JSON into the stdin of the contract process)

```json
{
    "hp_version": "<HotPocket_version>",
    "contract_id": "<contract_id>",
    "public_key": "<public_key>",
    "private_key": "<private_key>",
    "timestamp": "<timestamp>",
    "readonly": "<readonly>",
    "lcl_seq_no": "<lcl_seq_no>",
    "lcl_hash": "<lcl_hash>",
    "npl_fd": "<npl_fd>",
    "control_fd": "<control_fd>",
    "user_in_fd": "<user_in_fd>",
    "users": "<users>",
    "unl": "<unl>",
  }

```
- contract_id - Contract identifier
- public_key - Public Key of the node
- private_key - Private Key of the node
- readonly - Whether this is a contract invocation or a read request
- timestamp - time related to the consensus round
- lcl_seq_no - Closed ledger sequence no. (Not available in read-only mode.)
- lcl_hash - Closed ledger hash (Not available in read-only mode.)
- npl_fd - NPL file descriptor for the current contract invocation
- control_fd - Control file descriptor for the current contract invocation.
- user_in_fd - User input file descriptor for the current contract invocation.
- users - List of connected clients
- unl - Unique Node List (Not available in read-only mode.)

## Control channel
Control messages are passed between [smart contract](concepts.md#smart-contract) and HotPocket via the control file descriptor. This file descriptor can be found in the contract execution context we discussed early. Smart contract can send predefined instructions to HotPocket using control messages as follows.

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

NOTE: This control messages should not exceed 128KB.

## Users/Clients channel

HotPocket smart contract maintains a single file descriptor for User inputs and separate file descriptors for User outputs. Currently, user messages can be sent and received in JSON or BSON formats.

### Read users' inputs
When a contract is executing, the collected [user inputs](concepts.md#user-inputs) by HotPocket are passed to the [smart contract](concepts.md#smart-contract) via the user input file descriptor.

If you check the `users` object that is passed inside the stdin arguments from the HotPocket consensus engine, it is according to the following format.


```
{
    ...
    "<public key user i>" : ["<output file descriptor for user i>", [<offset>, <length>]],
    "<public key user i+1>" : ["<output file descriptor for user i+1>", [<offset>, <length>]],
    ...
}
```

Here the user public key hex value is the key and the value is an array where the first element carries the output file descriptor and the second element carries the array which contains the offset and length of related to each user input.The relevant user input file descriptor is also passed within the stdin arguments.

Hence, when reading user inputs from the User input file descriptor (`user_in_fd` in stdin arguments), you have to use the message offset and length to separate each message.

### Send outputs
For the [user output](concepts.md#user-outputs) file descriptors, the smart contract receives a map of users with their file descriptors. The contract outputs are passed via each user's output file descriptor from the smart contract.You can observe the locality of the file descriptor in the above described format.

When sending messages to the users, HotPocket smart contract should serialize the message accordingly based on the type of the message and the protocol that we suppose to use (BSON or JSON). This serialized message is written into the user output file descriptor as an array of two Buffers. The first element of the buffer array contains the byte length of the serialized content (Message length is in [Big Indian format](https://www.ibm.com/docs/en/epfz/5.3?topic=control-bigendian-littleendian-attributes)). The other one is the serialized content.


<br><br>

The user protocol is described in the [User Protocol](reference-client-protocol.md) section. Hence you can refer that, if you want to know further details of user messaging feature.


## NPL (Node Party Line) channel
The NPL channel is used to interchange messages between [smart contracts](concepts.md#smart-contract) running on each and every node in the [cluster](concepts.md#hotpocket-cluster), where HotPocket acts as an intermediary. The smart contract sends NPL messages to HotPocket via the NPL file descriptor. Then HotPocket broadcasts the message to all the connected [UNL](concepts.md#unl---unique-node-list) nodes in the cluster. Furthermore, the NPL messages sent by other peers are also sent to the smart contract from HotPocket using this file descriptor.<br><br> The NPL message can be in any format that needs to be transferred between peers. This channel can be used if we cannot assure that all the smart contracts in the cluster nodes are generating the same particular result (e.g.- random number). Hence, using this channel we can share the result between all the nodes and elect one result upon agreement.


### Read NPL messages.
In the HotPocket consensus engine side, it reads the corresponding file descriptor and obtains the message, and broadcasts it to the UNL. However, the node is an OBSERVER or a non-UNL node, it does not broadcast the NPL messages. This role is defined under HotPocket configuration file `hp.cfg`.

Once a node receives NPL messages, hose NPL inputs are fed into the contract side as sequence packets. It first sends the HEX `public key` buffer which is having a length of 33 bytes (66 chars. = 2 chars. for key type prefix + 64 chars. for key) and then the `data` buffer (`<pub_key><data>`).

### Send NPL messages.
When sending a message,it is written the content into the NPL file descriptor. This message length also should not exceed the 128KB limit. You can write inputs like text inputs and `JSON` objects in to this file descriptor as buffers.


## Note :
>The `patch.cfg` file.
Normally in each consensus round the contract block of _hp.cfg_ is synced with the peers in the cluster. That separated block is maintained inside the state directory of the node and it is called as patch.cfg file. There can be situations we have to update the patch.cfg and also retrieve relevant configurations from that configuration file withing the smart contract.