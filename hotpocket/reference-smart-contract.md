# HotPocket references - Smart Contract

HotPocket defines the smart contract as a regular POSIX application that can receive Inputs, produce Outputs and also persist State to permanent storage. You can use any POSIX-compliant programming platform to create HotPocket smart contracts. If you are interested, you can also develop a library that helps to execute the HotPocket features efficiently.

Currently, there are two such libraries are available for [NodeJS](https://github.com/HotPocketDev/hp-nodejs-contract) and [C](https://github.com/HotPocketDev/hp-c-contract).

The HotPocket smart contract and HotPocket consensus engine perform the communication via [File Descriptors](https://www.ibm.com/docs/en/aix/7.1?topic=volumes-using-file-descriptors). As per the IBM definition, the file descriptor is an unsigned integer used by a process to identify an open file. In HotPocket there are a set of application-specific file descriptors.
Those are,
- Control file descriptor
- User input file descriptor
- User output file descriptors
- NPL file descriptor

___Hence, you need some kind of knowledge of `File Descriptors` as a prerequisite.___

With that understanding, let's move on to the concepts you have to understand to develop a HotPocket smart contact for another programming language from scratch. Following are the basic components of the HotPocket smart contract.

- Control channel
- Contract execution context
- NPL Channel
- UNL collection
- User collection


## Control channel

A control channel should be there in every HotPocket smart contract, which helps to handle the control messages. This channel also uses a separate file descriptor. So that HotPocket smart contract can communicate control messages to HotPocket consensus engine via that.

The communication protocol of control messaging among connected nodes is Sequence Packet Protocol ([SPP](https://sites.ualberta.ca/dept/chemeng/AIX-43/share/man/info/C/a_doc_lib/aixprggd/progcomc/xns_seqprot.htm)). It is a byte-stream protocol used to support the SOCK_STREAM abstraction. Hence, in HotPocket there is an `upper bound` for the sequence packets maximum of 128KB.

You can refer [control messages protocol](reference-client-protocols.md/#control-messages) for further information.

## Contract execution context

HotPocket consensus engine passes context parameters in `JSON` format as a command line arguments to the contract when it's being executed. (The engine actually writes the contract arguments JSON into the stdin of the contract process)

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

### The `patch.cfg` file.
As it is known, in each consensus round the contract block of _hp.cfg_ is synced with the peers in the cluster. That separated block is maintained inside the state directory of the node and it is called as patch.cfg file. There can be situations we have to update the patch.cfg and also retrieve relevant configurations from that configuration file.

Hence, when defining the contact execution context you can consider all above aspects and come up with a suitable structure for the context.

## NPL Channel
Similar to the control channel of a contract there is a NPL channel that takes care of the NPL messaging of the smart contract. This also holds a file descriptor to communicate with the HotPocket consensus engine.
The communication protocol of NPL messaging among connected nodes is Sequence Packet Protocol ([SPP] as control messaging. Hence the upper bound limit for the sequence packet size will be applied here as well.

### Smart contract to HotPocket consensus engine.
When a message is sent, it is converted into a buffer and then it needs to check the size of the buffer does not exceed the previously mentioned upper limit. Then once that is satisfied, the message is written the content into the NPL file descriptor.

### HotPocket consensus engine to Smart contract.
In the HotPocket consensus engine side, it reads the corresponding file descriptor and obtains the message, and broadcasts it to the UNL via SSP. If the node is an OBSERVER or a non-UNL node, it does not broadcast the NPL messages.

Once a node receives NPL messages, they are queued and written into the NPL file descriptor of the destination node. Those NPL inputs are fed into the contract side as sequence packets. It first sends the `public key` buffer and then the `data` buffer (`<pub_key><data>`). Once the smart contract receives inputs, NPL message object is constructed and the event is emitted accordingly.

## UNL collection.
Under this, we can implement the UNL-related info retrieval as well as set up NPL message sending for the nodes. Here we can call the method in the NPL channel that writes data to the NPL file descriptor. Here, it will be convenient if we maintain a list of nodes with their public keys and statuses(active or inactive) which will be provided by HotPocket consensus engine side.

## User collection.
Under this section, we can manage the clients who have connected to this smart contract. When considering the user inputs and user outputs. HotPocket smart contract maintains a single file descriptor for User inputs and separate file descriptors for User outputs. Hence, now it is clear that we have to maintain a separate communication channel for each user. You can refer [Client Protocols](reference-client-protocols/../reference-client-protocols.md) further information. Currently, client/user messages can be sent and received in JSON or BSON formats.

If you check the `users` object that is passed from the HotPocket consensus engine, it is according to the following format.
```json
{
    ...
    "<public key user i>" : ["<output file descriptor for user i>", [<offset>, <length>]],
    "<public key user i+1>" : ["<output file descriptor for user i+1>", [<offset>, <length>]],
    ...
}
```
Hence, when reading user/client inputs from the User input file descriptor, you have to use the message offset and length to separate each message.

When sending messages to the users, HotPocket smart contract should serialize the message accordingly based on the type of the message and the protocol that we suppose to use (BSON or JSON). This serialized message is written into the user output file descriptor as an array of two Buffers. The first element of the buffer array contains the byte length of the serialized (Message length is in [Big Indian format](https://www.ibm.com/docs/en/epfz/5.3?topic=control-bigendian-littleendian-attributes)). The other one is the serialized content.)

