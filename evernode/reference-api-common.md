# Evernode defaults

## Override Evernode defaults - `set(newDefaults)`
Override Evernode default configs.

### Parameters
| Name        | Type   | Description                                                                                                                                                                                                                                               |
| ----------- | ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| newDefaults | object | Configurations to override  `{ governorAddress: '{string} governor xrpl address', rippledServer: '{string} rippled server url', xrplApi: '{XrplApi} xrpl instance', stateIndexId: '{string} firestore index', networkID: '{number} rippled network id' }` |

### Response format
This is a void function.

### Example
```javascript
    Defaults.set({
        governorAddress: 'rGVHr1PrfL93UAjyw3DWZoi9adz2sLp2yL'
    });
```

## Read Evernode defaults - `get()`
Read Evernode default configs.

### Response format
The Object of Evernode configs

### Example
```javascript
    const defaults = Defaults.get();
```
<br>

# Evernode clients

There are clients for registry and tenant in the Evernode library.
- [Governor Client](reference-api-governor.md)
- [Registry Client](reference-api-registry.md)
- [Heartbeat Client](reference-api-heartbeat.md)
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
    await client.disconnect();
```
<br>

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
<br>

## Attach the listener - `on(event, handler), once(event, handler)`
Listens to the subscribed [events](reference-api-events.md).
- `on` function will listen for the event without detaching the handler until it's [`off`](#detach-the-listener---offevent-handler--null).
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

## Detach the listener - `off(event, handler = null)`
Detaches the listener event.

### Parameters
| Name               | Type            | Description                                                                                                |
| ------------------ | --------------- | ---------------------------------------------------------------------------------------------------------- |
| event              | string          | [Event name](reference-api-events.md).                                                                     |
| handler (optional) | function(event) | Can be sent if a specific handler need to be detached. All the handlers will be detached if not specified. |

### Example
```javascript
    client.off(EvernodeEvents.HostRegistered);
```
<br>

## Check EVR balance - `async getEVRBalance()`
Gets the EVR balance in the registry account.

### Response format
Returns the available EVR amount as a `string`.

### Example
```javascript
    const balance = await client.getEVRBalance();
```
<br>

## Get the moment - `async getMoment(index = null)`
Get the moment from the given index (timestamp).

### Parameters
| Name             | Type   | Description                                |
| ---------------- | ------ | ------------------------------------------ |
| index (optional) | number | Index (timestamp) to get the moment value. |

### Response format
The moment of the given index (timestamp) as 'number'. Returns current moment if index (timestamp) is not given.

### Example
```javascript
    const moment = await client.getMoment();
```
<br>

## Get the moment start index - `async getMomentStartIndex(index = null)`
Get start index (timestamp) of the moment.

### Parameters
| Name             | Type   | Description                                      |
| ---------------- | ------ | ------------------------------------------------ |
| index (optional) | number | Index (timestamp) to get the moment start index. |

### Response format
Returns The index (timestamp) of the moment as a 'number'. Returns the current moment's start index (timestamp) if ledger index parameter is not given.

### Example
```javascript
    const startIdx = await client.getMomentStartIndex();
```
<br>

## Refresh the evernode config - `async refreshConfig()`
Loads the configs from XRPL hook and updates the in memory config.

### Response format
This is a void function.

### Example
```javascript
    await client.refreshConfig();
```
<br>

## Extract the event details from a XRPL transaction - `async extractEvernodeEvent(tx)`
Extracts the transaction info from a given transaction.

### Parameters
| Name | Type   | Description                                                                                                                                             |
| ---- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| tx   | object | Transaction to be deserialized and extracted. _Note: You need to deserialize Memos and HookParameters before passing the transaction to this function._ |

### Response format
Returns the event object in the format `{name: '', data: {}}`. Returns null if not handled.
```
{
    name: 'HostRegUpdated',
    data: {
        transaction: {
            Account: 'rKrSVLgaKQANTSEv1bY4cT4PVThCzFXpX6',
            Amount: '1',
            Destination: 'rQUhXd7sopuga3taru3jfvc1BgVbscrb1X',
            Fee: '11872',
            Flags: 0,
            HookParameters: [Array],
            LastLedgerSequence: 3872428,
            Memos: [],
            NetworkID: 21338,
            Sequence: 2996616,
            SigningPubKey: '0309EAAD262DD00DFD62583BDBBE2CC1C599A6C4BD9D1009AACE65DF36D77FD3B5',
            TransactionType: 'Payment',
            TxnSignature: '304502210081A9B31F86330FEC74B5AAC6480ECD579AFB08E90286F99BE8F09C8484130D1C0220022572EA86A974A23C64221885C4A9E9C88B2FDEB4F98E74FF00927529801C93',
            date: 739869360,
            hash: 'C9807D7B23DF7017F0A24A6C22FDE27655AC17EDFF70328B0F3E5D77A3D47ADA',
            inLedger: 3872420,
            ledger_index: 3872420
        },
        host: 'rKrSVLgaKQANTSEv1bY4cT4PVThCzFXpX6'
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
    tx.Memos = TransactionHelper.deserializeMemos(tx?.Memos);
    tx.HookParameters = TransactionHelper.deserializeHookParams(tx?.HookParameters);
    const extracted = await client.extractEvernodeEvent(tx);
```
<br>

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