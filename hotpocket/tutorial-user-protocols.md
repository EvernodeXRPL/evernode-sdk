# HotPocket tutorial - User protocols
Users communicate with Hotpocket via a [WebSocket](https://en.wikipedia.org/wiki/WebSocket). There are no direct communication between [users](concepts.md#users) and [smart contract](concepts.md#smart-contract). The user communicates with HotPocket and HotPocket serves as a middleman between user - smart contract communication. There're special protocols for this communication.<br><br> Every message that's being send to/from HotPocket contains a type field. User messages can be send and received in [json](https://en.wikipedia.org/wiki/JSON) or [bson](https://en.wikipedia.org/wiki/BSON) formats. The format is decided when the initial handshake with the user. The handshake messages will be in json format. The message protocols are described below in detailed manor.

## Initiating an user connection

### User challenge
In establishing user connection, When an user establishes a WebSoket connection to HotPocket, user challenge is sent to the user from HotPocket.
This message includes HotPocket version, Running smart contract's id, Running smart contract's version and a user challenge hex string.
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
Then user signs the challenge and sends back the handshake response with signature of user challenge, public key, a server challenge hex string and protocol to HotPocket.
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
And when receives the handshake response HotPocket verifies the handshake signature and accepts user connection and sends server challenge response to the user with signature of concatenated server challenge + contract id + contract version, public key and list of hex public key [UNL](concepts.md#unl---unique-node-list). And from the user handshake request HotPocket decides the message protocol (json|bson).
```
    {
        "type": "server_challenge_response",
        "sig": "<hex encoded signature of the [challenge + contract_id + contract version]>",
        "pubkey": "<our public key in hex>",
        "unl": [<hex unl pubkey list>]
    }
```

## Requesting connected HotPocket node's status
Users can request connected HotPocket node's status

### Status request
Connected users can request the status from HotPocket node. Buy sending a status request.
```
    {
        "type": "stat"
    }
```

HotPocket will send back the status response with the details of the connected node. This includes the [LCL](concepts.md#lcl---last-closed-ledger) sequence number and hash as well
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
Users can request connected HotPocket node's [LCL](concepts.md#lcl---last-closed-ledger).

### LCL request
User can ask current ledger info from HotPocket node.
```
    {
        "type": "lcl"
    }
```

### LCL response
HotPocket will send LCL response with the current ledger sequence and hash.
```
    {
        "type": "lcl_response",
        "ledger_seq_no": <lcl sequence no>,
        "ledger_hash": "<lcl hash hex>"
    }
```

## User inputs
[User inputs](concepts.md#user-inputs) are signed with user public key and sent to HotPocket along with input container and the signature. User inputs are subjected to [consensus](concepts.md#consensus).

### Input container
Input is sent with a nonce and maximum [ledger sequence](concepts.md#lcl---last-closed-ledger) for the input to accept.
```
    {
        "input": "<any string>",
        "nonce": <integer>, // Indicates input ordering.
        "max_ledger_seq_no": <integer>
    }
```

### Signed input
Input container is stringified and signed. Then sent both input and the signature to HotPocket.
```
    {
        "type": "contract_input",
        "input_container": "<stringified json input container>",
        "sig": "<hex encoded signature of stringified input container>"
    }
```

### User input status
When HotPocket receives a user input, HotPocket sends a input status message stating the status of the input. If the input is rejected the reason is sent with the message. Ledger sequence number and ledger hash is included when input is accepted.
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
HotPocket sends the [user output](concepts.md#user-outputs) with [ledger](concepts.md#lcl---last-closed-ledger) sequence number, ledger hash, outputs, hash of the outputs, hash tree and [UNL](concepts.md#unl---unique-node-list) signature.
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
Users send [read requests](concepts.md#read-requests) when they need immediate response. Read requests execute contract in read only mode and returns output to the user.

### Read request
This contains input content and an id.
```
    {
        "type": "contract_read_request",
        "id": "<any string>",
        "content": "<any string>"
    }
```

### Read response
Response for the read request is sent as a read response from HotPocket to the user. This includes request id as reply for field and the response content.
```
    {
        "type": "contract_read_response",
        "reply_for": "<corresponding request id>",
        "content": "<response string>"
    }
```

## Listening to the HotPocket notifications
Users can subscribe to HotPocket notifications and receive them.

### Subscription event request
Subscription event request is sent from user to HotPocket to subscribe for notifications. This states which channel to subscribe and whether this is to enable or disable the subscription.
```
    {
        "type": "subscription",
        "channel": "unl_change" | "ledger_event" | "health_event",
        "enabled": true | false
    }
```

### UNL change notification
[UNL](concepts.md#unl---unique-node-list) change notification is sent to users from HotPcket when UNL is changed. User can subscribe to this notification by sending subscription request with unl_change channel.
```
    {
        "type": "unl_change",
        "unl": ["<pubkey1>{[ed prefix][64 characters]}", ...] // Hex pubkey list of unl nodes.
    }
```

### Ledger created notification
Ledger created notification is sent to users when ledger created in HotPocket. User can subscribe to this notification by sending subscription request with ledger_event channel.
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
Sync status notification is sent to users when HotPocket node sync status is changed. User can subscribe to this notification by sending subscription request with ledger_event channel.
```
    {
        "type": "ledger_event",
        "event": "vote_status",
        "vote_status": "synced" | "desync" | "unreliable"
    }
```

### Health notification
Health notification is sent to users from HotPocket when health status is changed. User can subscribe to this notification by sending subscription request with health_event channel.
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
Users can query the ledger info and request filter out the ledger info.

### Ledger query request
This is sent from user to HotPocket requesting ledger info. HotPocket currently support filter by [seq_no](concepts.md#lcl---last-closed-ledger) only.
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
This is sent as the response for ledger query request. Inputs and outputs are populated if exist for the ledger.
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

Next: [HotPocket contract protocols](tutorial-contract-protocols.md)