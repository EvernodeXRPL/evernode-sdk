# HotPocket references - Contract protocol

HotPocket defines a smart contract as a regular POSIX application that can receive Inputs, produce Outputs and also persist State to permanent storage. You can use any POSIX-compliant programming platform to create HotPocket smart contracts. Furthermore, you can also develop a library that helps to execute the HotPocket features efficiently.

Currently, there are two such libraries available for [NodeJS](https://github.com/HotPocketDev/hp-nodejs-contract) and [C](https://github.com/HotPocketDev/hp-c-contract).

The HotPocket [smart contract](concepts.md#smart-contract) is spawned as a child process by HotPocket for each [consensus](concepts.md#consensus) round. The communication between the smart contract and the HotPocket consensus engine happens as an inter-process communication using [file descriptors](https://en.wikipedia.org/wiki/File_descriptor). As per the IBM definition, the file descriptor is an unsigned integer used by a process to identify an open file. In HotPocket, there is a set of application-specific file descriptors, which are used for different channels. The file descriptors that are currently defined are given below (each and every message protocol will be described later):
- User input/output file descriptor
- NPL file descriptor
- Control file descriptor

When implementing a smart contract on the HotPocket consensus engine, you need to consider the following basic components of the HotPocket smart contract:

  - [Contract execution context](#contract-execution-context)
  - [Control channel](#control-channel)
  - [Users/Clients channel](#usersclients-channel)
  - [NPL (Node Party Line) channel](#npl-node-party-line-channel)

Let's get a basic understanding of these components.

## Contract execution context

The HotPocket consensus engine passes context parameters in `JSON` format as command line arguments to the contract when it is being executed (the engine actually writes the contract arguments JSON into the stdin of the contract process).

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
Control messages are passed between the [smart contract](concepts.md#smart-contract) and HotPocket via the control file descriptor. This file descriptor can be found in the contract execution context as discussed earlier. The smart contract can send predefined instructions to HotPocket using control messages as follows.

### Terminating the contract
Smart contracts can terminate execution by sending the 'contract_end' control message to the HotPocket node.

    {
        'type': 'contract_end'
    }


### Changing peers
Smart contracts can add or remove peers from the HotPocket node by sending the 'peer_changeset' control message. After receiving this message, HotPocket will add the peers in the `add` section to the peer list, and remove the peers in the `remove` section from the peer list.

    {
        'type': 'peer_changeset',
        'add': ['<ip1>:<port1>', '<ip2>:<port2>', ...],
        'remove': ['<ip1>:<port1>', '<ip2>:<port2>', ...]
    }

NOTE: Control messages should not exceed 128KB.

## Users/Clients channel

The HotPocket smart contract maintains a single file descriptor for user inputs and separate file descriptors for user outputs. Currently, user messages can be sent and received in JSON or BSON formats.

### Read users' inputs
When a contract is executing, the collected [user inputs](concepts.md#user-inputs) by HotPocket are passed to the [smart contract](concepts.md#smart-contract) via the user input file descriptor.

The `users` object that is passed inside the stdin arguments from the HotPocket consensus engine will be in the following format:

```
{
    ...
    "<public key user i>" : ["<output file descriptor for user i>", [<offset>, <length>]],
    "<public key user i+1>" : ["<output file descriptor for user i+1>", [<offset>, <length>]],
    ...
}
```

The key will be the user public key hex value, while the value is an array where the first element carries the output file descriptor and the second element carries the array which contains the offset and length of each user input. The relevant user input file descriptor is also passed within the stdin arguments.

In this way, when reading user inputs from the user input file descriptor (`user_in_fd` in stdin arguments), you have to use the message offset and length to separate each message.

### Send outputs
For the [user output](concepts.md#user-outputs) file descriptors, the smart contract receives a map of users with their file descriptors. The contract outputs are passed via each user's output file descriptor from the smart contract. The positioning of the output file descriptors inside the `users` object can be observed in the previous section which is about  `reading users' inputs`.

When sending messages to the users, the HotPocket smart contract should serialize the message accordingly based on the type of the message and the protocol that is used (BSON or JSON). This serialized message is written into the user output file descriptor as an array of two buffers. The first element of the buffer array contains the byte length of the serialized content (message length is in [Big Endian format](https://www.ibm.com/docs/en/epfz/5.3?topic=control-bigendian-littleendian-attributes)). The other element will be the serialized content.

```
[
    <4 byte Header Buffer - contains the length (Big Endian format)>,
    <Message Buffer - serialized message>
]
```

<br>

Please refer the user protocol described in the [User Protocol](reference-client-protocol.md) section to get further details of the user messaging feature.

## NPL (Node Party Line) channel
The NPL channel is used to interchange messages between [smart contracts](concepts.md#smart-contract) running on each and every node in the [cluster](concepts.md#hotpocket-cluster), where HotPocket acts as an intermediary. The smart contract sends NPL messages to HotPocket via the NPL file descriptor, and HotPocket broadcasts the message to all the connected [UNL](concepts.md#unl---unique-node-list) nodes in the cluster. Furthermore, the NPL messages sent by other peers are also sent to the smart contract from HotPocket using this file descriptor.<br><br> The NPL message can be in any format that needs to be transferred between peers. This channel can be used if we cannot assure that all the smart contracts in the cluster nodes are generating the same particular result (e.g.- random number). Hence, using this channel we can share the result between all the nodes and elect one result upon agreement.

### Read NPL messages
The HotPocket consensus engine reads the corresponding file descriptor, obtains the message, and broadcasts it to the UNL. However, the node is an OBSERVER or a non-UNL node, which means it does not broadcast the NPL messages. This role is defined in the HotPocket configuration file [hp.cfg](reference-configuration.md).

Once a node receives NPL messages, the NPL inputs are fed into the contract as sequence packets. It first sends the HEX `public key` buffer which has a length of 33 bytes (66 chars. = 2 chars. for key type prefix + 64 chars. for key) and then the `data` buffer (`<pub_key><data>`).

### Send NPL messages
When sending a message, the content is written into the NPL file descriptor. This message length should also not exceed 128KB. You can write inputs such as text inputs and `JSON` objects (after converting into strings) to this file descriptor as buffers.

## Note :
> The `patch.cfg` file -
In each consensus round, the contract block of _hp.cfg_ is synced with the peers in the cluster. The separated block is maintained inside the state directory of the node and is called the patch.cfg file. In some instances, we might need to update the patch.cfg and retrieve relevant configurations from that configuration file within the smart contract.