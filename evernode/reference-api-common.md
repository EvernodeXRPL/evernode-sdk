# Evernode clients

There are clients for registry and tenant in the Evernode library.
- [Registry Client](reference-api-registry.md)
- [Tenant Client](reference-api-tenant.md)

## Connect to the client - `async connect()`
Connects the client to XRPL server and do the config loading and subscriptions.
- [subscribe](#subscribe-to-the-events---async-subscribe) is called inside this.

### Response format
Returns boolean. `true` if success.

### Example
```javascript
    const status = await client.connect();
```


## Terminate the client - `async disconnect()`
Disconnects the client to XRPL server and do the un-subscriptions.
- [unsubscribe](#unsubscribe-from-the-events---async-unsubscribe) is called inside this.

### Response format
This is a void function.

### Example
```javascript
    const status = await client.disconnect();
```


## Subscribe to the events - `async subscribe()`
Subscribes to the client [events](reference-api-events.md).

### Response format
This is a void function.

### Example
```javascript
    await client.subscribe();
```


## Unsubscribe from the events - `async unsubscribe()`
Unsubscribes from the client [events](reference-api-events.md).

### Response format
This is a void function.

### Example
```javascript
    await client.unsubscribe();
```


## Attach the listener - `on(event, handler), once(event, handler)`
Listens to the subscribed [events](reference-api-events.md).
- `on` function will listen for the event without detaching the handler until it's [`off`](#deattach-the-listener---offevent-handler--null).
- `once` function will listen only once and detach the handler.

### Parameters
| Name    | Type            | Description                            |
| ------- | --------------- | -------------------------------------- |
| event   | string          | [Event name](reference-api-events.md). |
| handler | function(event) | Callback function to handle the event. |

### Example
```javascript
    client.on(EvernodeEvents.HostRegistered, (ev) => {});
    client.once(EvernodeEvents.HostRegistered, (ev) => {});
```


## Deattach the listener - `off(event, handler = null)`
Deattachs the listener event.

### Parameters
| Name               | Type            | Description                                                                                                    |
| ------------------ | --------------- | -------------------------------------------------------------------------------------------------------------- |
| event              | string          | [Event name](reference-api-events.md).                                                                         |
| handler (optional) | function(event) | Can be sent if a specific handler need to be detached. All the handlers will be detached if not specified. |

### Example
```javascript
    client.off(EvernodeEvents.HostRegistered);
```


## Check EVR balance - `async getEVRBalance()`
Gets the EVR balance in the registry account.

### Response format
Returns the available EVR amount as a `string`.

### Example
```javascript
    const balance = await client.getEVRBalance();
```


## Get the moment - `async getMoment()`
Gets the moment from the given XRPL index. (1 Moment - 1190 XRP ledgers).

### Parameters
| Name                   | Type   | Description                           |
| ---------------------- | ------ | ------------------------------------- |
| ledgerIndex (optional) | number | Ledger index to get the moment value. |

### Response format
Returns the moment of the given XPR ledger index as `number`. Returns current moment if ledger index is not given.

### Example
```javascript
    const moment = await client.getMoment();
```


## Get the moment start index - `async getMomentStartIndex()`
Gets start XRP ledger index of the moment (of the given XRPL index).

### Parameters
| Name                   | Type   | Description                           |
| ---------------------- | ------ | ------------------------------------- |
| ledgerIndex (optional) | number | Ledger index to get the moment value. |

### Response format
Returns the XRP ledger index of the moment (of the given XRPL index) as `number`. Returns the current moment's start XRP ledger index if ledger index parameter is not given.

### Example
```javascript
    const startIdx = await client.getMomentStartIndex();
```


## Refresh the evernode config - `async refreshConfig()`
Loads the configs from XRPL hook and updates the in memory config.

### Response format
This is a void function.

### Example
```javascript
    await client.refreshConfig();
```


## Extract the event details from a XRPL transaction - `async extractEvernodeEvent(tx)`
Gets start XRP ledger index of the moment (of the given XRPL index).
_Note: You need to deserialize memos before passing the transaction to this function._

### Parameters
| Name | Type   | Description                                   |
| ---- | ------ | --------------------------------------------- |
| tx   | object | Transaction to be deserialized and extracted. |

### Response format
Returns the event object in the format `{name: '', data: {}}`. Returns null if not handled.
```
{
    name: 'HostRegUpdated',
    data: {
        transaction: {
            Account: 'rJ2Vwa7PKR2VviWbZoW6nMVhd2nUTQGrvE',
            Amount: '1',
            Destination: 'r3cNR2bdao1NyvQ5ZuQvCUgqkoWGmgF34E',
            Fee: '587',
            Flags: 0,
            LastLedgerSequence: 6321792,
            Memos: [Array],
            Sequence: 3898010,
            SigningPubKey: '02003CB922F13E4880C542D87B02852F19039347115DC7370C157D9896E65AB690',
            TransactionType: 'Payment',
            TxnSignature: '3044022054EA456D5644A40CA0309A4F449E829695B2B4BEA8050AF42DAE9BF44C0BA4C5022066AC7328583529BAD91D10EC49B1405ED7AE7D862BD3036B8F31F6A2D6415BC9',
            date: 717667781,
            hash: 'B1A93A4F209F0EDD55A6ED42A96B545D1AD425592191F64B61611765B727B31D',
            inLedger: 6321784,
            ledger_index: 6321784
        },
        host: 'rJ2Vwa7PKR2VviWbZoW6nMVhd2nUTQGrvE',
        version: '0.5.10',
        specs: [ '', '', '', '', '', '', '0', '', '0.5.10' ]
    }
}
```
| Name        | Type   | Description                                   |
| ----------- | ------ | --------------------------------------------- |
| name        | string | [Event name](#events).                        |
| transaction | object | The original transaction from the XRP ledger. |
- There will be more properties in the response which are according to the event type.

### Example
```javascript
    tx.Memos = evernode.TransactionHelper.deserializeMemos(tx.Memos);
    const startIdx = await client.extractEvernodeEvent(tx);
```


## Prune dead hosts - `async pruneDeadHost(hostAddress)`
Remove a host which is inactive for a long period. The inactivity is checked by Evernode itself and only pruned if inactive thresholds are met.

### Parameters
| Name        | Type   | Description                            |
| ----------- | ------ | -------------------------------------- |
| hostAddress | string | XRPL address of the host to be pruned. |

### Response format
This is a void function.

### Example
```javascript
    await client.pruneDeadHost('rPvhbE9hNgSCb6tgMCoDwsxRgewxcvD7jk');
```