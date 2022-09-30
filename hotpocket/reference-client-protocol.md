# HotPocket references - Client (User) protocol
Users communicate with Hotpocket via [WebSockets](https://en.wikipedia.org/wiki/WebSocket). There is no direct communication between [users](concepts.md#users) and [smart contracts](concepts.md#smart-contract). The user communicates with HotPocket, and HotPocket serves as an intermediary between the user and the smart contract. There are special protocol for this communication.<br><br> Every message that is being sent to and from HotPocket contains a type field. User messages can be sent and received in [json](https://en.wikipedia.org/wiki/JSON) or [bson](https://en.wikipedia.org/wiki/BSON) formats. The format is decided when the initial handshake happens with the user. The handshake messages will be in json format. The message protocol are described below in a detailed manner.

## Initiating a user connection

### User challenge
When a user establishes a WebSoket connection to HotPocket, a user challenge is sent to the user from HotPocket.
This message includes the HotPocket version, the running smart contract's id, the running smart contract's version, and a user challenge hex string.
```
    {
        "hp_version": "<hp protocol version>",
        "type": "user_challenge",
        "contract_id": "<contract id>",
        "contract_version": "<contract version string>",
        "challenge": "<challenge string>"
    }
```

### User handshake response
The user then signs the challenge and sends back the handshake response to HotPocket with the following data: the signature of the user challenge, the public key, a server challenge hex string, and the protocol.
```
    {
        "type": "user_challenge_response",
        "sig": "<hex signature of the challenge>",
        "pubkey": "<hex public key of the user>",
        "server_challenge": "<hex encoded challenge issued to server>", (max 16 bytes/32 chars)
        "protocol": "<json | bson>"
    }
```

### Server challenge response
Upon receiving the handshake response, HotPocket verifies the handshake signature and accepts the user connection and sends the server challenge response to the user with the following data: the signature of the concatenated (server challenge + contract id + contract version), the public key, and a list of hex public keys ([UNL](concepts.md#unl---unique-node-list)). HotPocket decides the message protocol (json|bson) from the user's handshake request.
```
    {
        "type": "server_challenge_response",
        "sig": "<hex encoded signature of the [challenge + contract_id + contract version]>",
        "pubkey": "<our public key in hex>",
        "unl": [<hex unl pubkey list>]
    }
```

## Requesting connected HotPocket node's status
Users can request the connected HotPocket node's status.

### Status request
Connected users can request the status from the HotPocket node by sending a status request.
```
    {
        "type": "stat"
    }
```

HotPocket will send back the status response with the details of the connected node. This includes the [LCL](concepts.md#lcl---last-closed-ledger) sequence number and hash.
### Status response
```
    {
        "type": "stat_response",
        "hp_version": "<version>",
        "ledger_seq_no": <lcl sequence no>,
        "ledger_hash": "<lcl hash hex>",
        "vote_status": "synced" | "desync" | "unreliable",
        "roundtime": <roundtime milliseconds>,
        "contract_execution_enabled": true | false,
        "read_requests_enabled": true | false,
        "is_full_history_node": true | false,
        "weakly_connected": true | false,
        "current_unl": [ "<ed prefixed pubkey hex>"", ... ],
        "peers": [ "ip:port", ... ]
    }
```

## Requesting connected HotPocket node's LCL
Users can request the connected HotPocket node's [LCL](concepts.md#lcl---last-closed-ledger).

### LCL request
Users can request the current ledger's info from the HotPocket node by sending a LCL request.
```
    {
        "type": "lcl"
    }
```

### LCL response
HotPocket will send the LCL response with the current ledger sequence and hash.
```
    {
        "type": "lcl_response",
        "ledger_seq_no": <lcl sequence no>,
        "ledger_hash": "<lcl hash hex>"
    }
```

## User inputs
[User inputs](concepts.md#user-inputs) are signed with the user's public key and sent to HotPocket along with the input container and the signature. User inputs are subjected to [consensus](concepts.md#consensus).

### Input container
The input is sent with a nonce and maximum [ledger sequence](concepts.md#lcl---last-closed-ledger) for the input to accept.
```
    {
        "input": "<any string>",
        "nonce": <integer>, // Indicates input ordering.
        "max_ledger_seq_no": <integer>
    }
```

### Signed input
The input container is stringified, signed, and then both the input and the signature are sent to HotPocket.
```
    {
        "type": "contract_input",
        "input_container": "<stringified json input container>",
        "sig": "<hex encoded signature of stringified input container>"
    }
```

### User input status
When HotPocket receives a user input, HotPocket sends an input status message stating the status of the input. If the input is rejected, the reason is sent along with the message. The ledger sequence number and ledger hash are included when the input is accepted.
```
    {
        "type": "contract_input_status",
        "status": "<accepted|rejected>",
        "reason": "<reason>",
        "input_hash": "<hex hash of original input signature>",
        "ledger_seq_no": <sequence no of the ledger that the input got included in>,
        "ledger_hash": "<hex hash no of the ledger that the input got included in>"
    }
```

### User output
HotPocket sends the [user output](concepts.md#user-outputs) with the [ledger](concepts.md#lcl---last-closed-ledger) sequence number, the ledger hash, the outputs, the hash of the outputs, the hash tree, and the [UNL](concepts.md#unl---unique-node-list) signature.
```
    {
        "type": "contract_output",
        "ledger_seq_no": <integer>,
        "ledger_hash": "<lcl hash hex>",
        "outputs": ["<output string 1>", "<output string 2>", ...], // The output order is the hash generation order.
        "output_hash": "<hex hash of user's outputs>",  [output hash = hash(pubkey+all outputs for the user)]
        "hash_tree": [<hex merkle hash tree>], // Collapsed merkle tree with user's hash element marked as null. 
        "unl_sig": [["<pubkey hex>", "<sig hex>"], ...] // UNL pubkeys and signatures of root hash.
    }
```

## Read request
Users send [read requests](concepts.md#read-requests) when they need an immediate response. Read requests execute contracts in read-only mode and returns an output to the user.

### Read request
This contains the input content and an id.
```
    {
        "type": "contract_read_request",
        "id": "<any string>",
        "content": "<any string>"
    }
```

### Read response
The response for the read request is sent as a read response from HotPocket to the user. This includes the request id in the 'reply_for' field, and the response content.
```
    {
        "type": "contract_read_response",
        "reply_for": "<corresponding request id>",
        "content": "<response string>"
    }
```

## Listening to HotPocket notifications
Users can subscribe to HotPocket notifications and receive them.

### Subscription event request
A subscription event request is sent from the user to HotPocket to subscribe for notifications. This states which channel to subscribe to, and whether this is to enable or disable the subscription.
```
    {
        "type": "subscription",
        "channel": "unl_change" | "ledger_event" | "health_event",
        "enabled": true | false
    }
```

### UNL change notification
A [UNL](concepts.md#unl---unique-node-list) change notification is sent to users from HotPcket when the UNL is changed. Users can subscribe to this notification by sending a subscription request with the 'unl_change' channel.
```
    {
        "type": "unl_change",
        "unl": ["<pubkey1>{[ed prefix][64 characters]}", ...] // Hex pubkey list of unl nodes.
    }
```

### Ledger created notification
A notification for ledger creation is sent to users when a ledger is created in HotPocket. Users can subscribe to this notification by sending a subscription request with the 'ledger_event' channel.
```
    {
        "type": "ledger_event",
        "event": "ledger_created",
        "ledger": {
            "seq_no": <lcl sequence no>,
            "timestamp": <lcl timestamp>,
            "hash": <lcl hash hex>,
            "prev_hash": <previous lcl hash hex>,
            "state_hash": <state hash hex>,
            "config_hash": <patch config hash hex>,
            "user_hash": <user list hash hex>,
            "input_hash": <user input hash hex>,
            "output_hash": <user output hash hex>
        }
    }
```

### Sync status notification
A notification for sync status is sent to users when a HotPocket node's sync status is changed. Users can subscribe to this notification by sending a subscription request with the 'ledger_event' channel.
```
    {
        "type": "ledger_event",
        "event": "vote_status",
        "vote_status": "synced" | "desync" | "unreliable"
    }
```

### Health notification
A health notification is sent to users from HotPocket when the health status is changed. Users can subscribe to this notification by sending a subscription request with the 'health_event' channel.
```
{
    "type": "health_event",
    "event": "proposal" | "connectivity",

    // if proposal
    "comm_latency": {min:0, max:0, avg:0},
    "read_latency": {min:0, max:0, avg:0}
    "batch_size": 0

    // if connectivity
    "peer_count": 0,
    "weakly_connected": true | false
}
```

## Querying the ledger info
Users can query the ledger info and request to filter out the ledger info.

### Ledger query request
This is sent from the user to HotPocket requesting the ledger info. HotPocket currently supports filter by [seq_no](concepts.md#lcl---last-closed-ledger) only.
```
    {
        "type": "ledger_query",
        "id": "<query id>",
        "filter_by": "seq_no",
        "params": {
            "seq_no": <lcl sequence no> 
        },
        "include": ["inputs", "outputs"]
    }
```

### Ledger query response
This is sent as the response for the ledger query request. Inputs and outputs are populated if they exist for the ledger.
```
    {
        "type": "ledger_query_result",
        "reply_for": "<original query id>",
        "error": "error_code" or NULL,
        "results": [{
            "seq_no": <lcl sequence no>,
            "timestamp": <lcl timestamp>,
            "hash": <lcl hash hex>,
            "prev_hash": <previous lcl hash hex>,
            "state_hash": <state hash hex>,
            "config_hash": <patch config hash hex>,
            "user_hash": <user list hash hex>,
            "input_hash": <user input hash hex>,
            "output_hash": <user output hash hex>,
            "inputs": [{
                "pubkey": <user public key>,
                "hash": <input hash>,
                "nonce": <integer>, // Indicates input ordering
                "bob": <input blob hex>
            }, ...],
            "outputs": [{
                "pubkey": <user public key>,
                "hash": <output hash>,
                "nonce": <integer>, // Indicates input ordering
                "bobs": [<output blob hex>, ...]
            }, ...]
        }, ...]
    }
```