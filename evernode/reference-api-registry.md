# Registry client
## RegistryClient class constructor - `RegistryClient(options = {})`

### Parameters
Takes on parameter `options` which is a JSON object of options that is passed to RegistryClient.
```
{
    registryAddress: 'r3cNR2bdao1NyvQ5ZuQvCUgqkoWGmgF34E',
    rippledServer: 'wss://hooks-testnet-v2.xrpl-labs.com'
}
```
| Name            | Type   | Description            |
| --------------- | ------ | ---------------------- |
| registryAddress | string | Registry XRPL address. |
| rippledServer   | string | Rippled server URL.    |

### Example
```javascript
    const registryClient = new RegistryClient({registryAddress: 'r3cNR2bdao1NyvQ5ZuQvCUgqkoWGmgF34E'});
```


## Prepare the registry client - `async connect()`
Connects the client to xrpl server and do the config loading and subscriptions.
- [subscribe](#subscribe-to-the-events---async-subscribe) is called inside this.

### Response format
Returns boolean. `true` if success.

### Example
```javascript
    const status = await registryClient.connect();
```


## Terminate the registry client - `async disconnect()`
Disconnects the client to xrpl server and do the unsubscriptions.
- [unsubscribe](#unsubscribe-from-the-events---async-unsubscribe) is called inside this.

### Response format
This is a void function.

### Example
```javascript
    const status = await registryClient.disconnect();
```


## Subscribe to the events - `async subscribe()`
Subscribes to the registry client events.

### Events
| Name                 | Description                                                           |
| -------------------- | --------------------------------------------------------------------- |
| HostRegistered       | Triggered when host registration event is received to the registry.   |
| HostDeregistered     | Triggered when host deregistration event is received to the registry. |
| HostRegUpdated       | Triggered when host sends an update info request.                     |
| Heartbeat            | Triggered when registry receives a heartbeat from a host.             |
| HostPostDeregistered | Triggered after the host is deregistered.                             |
| DeadHostPrune        | Triggered when dead host prone request is received to the registry.   |

### Response format
This is a void function.

### Example
```javascript
    await registryClient.subscribe();
```


## Unsubscribe from the events - `async unsubscribe()`
Unsubscribes from the registry client events.

### Response format
This is a void function.

### Example
```javascript
    await registryClient.unsubscribe();
```


## Attach the listener - `on(event, handler), once(event, handler)`
Listens to the subscribed events.
- `on` function will listen for the event without deattaching the handler until it's [`off`](#deattach-the-listener---offevent-handler--null).
- `once` function will listen only once and deattach the handler.

### Parameters
| Name    | Type            | Description                            |
| ------- | --------------- | -------------------------------------- |
| event   | string          | [Event name](#events).                 |
| handler | function(event) | Callback function to handle the event. |

### Example
```javascript
    registryClient.on(EvernodeEvents.HostRegistered, (ev) => {});
    registryClient.once(EvernodeEvents.HostRegistered, (ev) => {});
```


## Deattach the listener - `off(event, handler = null)`
Deattachs the listener event.

### Parameters
| Name               | Type            | Description                                                                                                    |
| ------------------ | --------------- | -------------------------------------------------------------------------------------------------------------- |
| event              | string          | [Event name](#events).                                                                                         |
| handler (optional) | function(event) | Can be sent if a specific handler need to be deattached. All the handlers will be deattached if not specified. |

### Example
```javascript
    registryClient.off(EvernodeEvents.HostRegistered);
```


## Get active hosts - `async getActiveHosts()`
Gets all the active hosts registered in Evernode without paginating.

### Response format
Returns the list of active hosts.
```
[
    {
        id: '455652030000000000000000FB5BCF72851F76EF3031DBC95ECC6544090C96E8',
        createTime: 2022-07-05T12:22:15.505Z,
        updateTime: 2022-09-27T10:40:37.520Z,
        cpuCount: 3,
        ramMb: 6576,
        registrationLedger: 3904936,
        lastHeartbeatLedger: 6296292,
        maxInstances: 6,
        nfTokenId: '000800005372A50EC3F94F48AFD93548F10D33039A5A19FFFE25BCDE00000043',
        countryCode: 'US',
        activeInstances: 1,
        version: '0.5.10',
        registrationFee: 5120,
        address: 'rPvhbE9hNgSCb6tgMCoDwsxRgewxcvD7jk',
        cpuModelName: 'Intel(R) Xeon(R) CPU E5-2680 v2 2.80GHz',
        cpuMicrosec: 800000,
        description: '107.174.93.135',
        cpuMHz: 2800,
        diskMb: 44366,
        key: '455652030000000000000000FB5BCF72851F76EF3031DBC95ECC6544090C96E8',
        active: true
    },
    ...
]
```
| Name                | Type     | Description                                                        |
| ------------------- | -------- | ------------------------------------------------------------------ |
| id                  | string   | Identifier of the host. This is used for internal use in evernode. |
| createTime          | datetime | Host record created date and time                                  |
| updateTime          | datetime | Host record updated date and time                                  |
| cpuCount            | number   | CPU count of the host machine                                      |
| ramMb               | number   | Host machine's evernode allocated RAM in MBs                       |
| registrationLedger  | number   | Host machine registered XRP ledger                                 |
| lastHeartbeatLedger | number   | XRP ledger that the last heartbeat is received                     |
| maxInstances        | number   | Max number of instances that can be created in the host            |
| nfTokenId           | string   | Registration NFT ID of the host                                    |
| countryCode         | string   | Host machine's origin country code.                                |
| activeInstances     | number   | Currently allocated instance count in the host machine.            |
| version             | string   | Sashimono version installed in the host machine.                   |
| registrationFee     | number   | Registration fee paid by the host when it's registered.            |
| address             | string   | XRPL account address of the host.                                  |
| cpuModelName        | string   | CPU model of the host machine.                                     |
| cpuMicrosec         | number   | CPU time in micro seconds allocated for evernode.                  |
| description         | string   | IP address or the DNS of the host.                                 |
| cpuMHz              | number   | CPU speed of the host.                                             |
| diskMb              | number   | Disk space allocated for evernode in the host.                     |
| key                 | string   | Same as the `id`. Used for internal use.                           |
| active              | boolean  | Boolean indicating whether the host is active or not.              |

### Example
```javascript
    const activeHosts = await registryClient.getActiveHosts();
```


## Get all hosts - `async getHosts()`
Gets all the hosts registered in Evernode in paginated manor. The result's are paginated. Default page size is 20.
_Note: Specifing both filter and pagination does not supported. _

### Parameters
| Name                     | Type   | Description                                                                               |
| ------------------------ | ------ | ----------------------------------------------------------------------------------------- |
| filters (optional)       | object | Filter criteria to filter the hosts. The filter key can be a either property of the host. |
| pageSize (optional)      | number | Page size for the results.                                                                |
| nextPageToken (optional) | string | Next page's token, If received by the previous result set.                                |

### Response format
Returns the list of active hosts. The response will be in `{data: [], nextPageToken: ''}` only if there are more pages. Otherwise the response will only contain the host list. 
```
{
    data: [
        {
            id: '455652030000000000000000FB5BCF72851F76EF3031DBC95ECC6544090C96E8',
            createTime: 2022-07-05T12:22:15.505Z,
            updateTime: 2022-09-27T10:40:37.520Z,
            cpuCount: 3,
            ramMb: 6576,
            registrationLedger: 3904936,
            lastHeartbeatLedger: 6296292,
            maxInstances: 6,
            nfTokenId: '000800005372A50EC3F94F48AFD93548F10D33039A5A19FFFE25BCDE00000043',
            countryCode: 'US',
            activeInstances: 1,
            version: '0.5.10',
            registrationFee: 5120,
            address: 'rPvhbE9hNgSCb6tgMCoDwsxRgewxcvD7jk',
            cpuModelName: 'Intel(R) Xeon(R) CPU E5-2680 v2 2.80GHz',
            cpuMicrosec: 800000,
            description: '107.174.93.135',
            cpuMHz: 2800,
            diskMb: 44366,
            key: '455652030000000000000000FB5BCF72851F76EF3031DBC95ECC6544090C96E8',
            active: true
        },
        ...
    ],
    nextPageToken: 'AFTOeJxnyBn9E3UfEi8zjdFSAgaN10KEmhZSjrONUBsuLJ5PIPKj-nODfmiynybEf5zls9pUk9sDQORJUFkuheihqVcjdOAy-nLkHs3sjMOa0mHfsN_TE8EMdIo_6mzNtewem70pn0h_lB9kEOHiQqzvp1hLC692i7jVAxfH6G0teNeOdZP18m2RrJiHVROHebQdOMnCvXBJQEu6qmec2bwwb9zLe7wZ7GO9jfUfr-mb'
}
```
| Name                | Type     | Description                                                        |
| ------------------- | -------- | ------------------------------------------------------------------ |
| id                  | string   | Identifier of the host. This is used for internal use in evernode. |
| createTime          | datetime | Host record created date and time                                  |
| updateTime          | datetime | Host record updated date and time                                  |
| cpuCount            | number   | CPU count of the host machine                                      |
| ramMb               | number   | Host machine's evernode allocated RAM in MBs                       |
| registrationLedger  | number   | Host machine registered XRP ledger                                 |
| lastHeartbeatLedger | number   | XRP ledger that the last heartbeat is received                     |
| maxInstances        | number   | Max number of instances that can be created in the host            |
| nfTokenId           | string   | Registration NFT ID of the host                                    |
| countryCode         | string   | Host machine's origin country code.                                |
| activeInstances     | number   | Currently allocated instance count in the host machine.            |
| version             | string   | Sashimono version installed in the host machine.                   |
| registrationFee     | number   | Registration fee paid by the host when it's registered.            |
| address             | string   | XRPL account address of the host.                                  |
| cpuModelName        | string   | CPU model of the host machine.                                     |
| cpuMicrosec         | number   | CPU time in micro seconds allocated for evernode.                  |
| description         | string   | IP address or the DNS of the host.                                 |
| cpuMHz              | number   | CPU speed of the host.                                             |
| diskMb              | number   | Disk space allocated for evernode in the host.                     |
| key                 | string   | Same as the `id`. Used for internal use.                           |
| active              | boolean  | Boolean indicating whether the host is active or not.              |
| nextPageToken       | string   | Token reference for the next page.                                 |

### Example
```javascript
    const activeHosts = await registryClient.getHosts();
```


## Check EVR balance - `async getEVRBalance()`
Gets the EVR balance in the registry account.

### Response format
Returns the available EVR amount as a `string`.

### Example
```javascript
    const balance = await registryClient.getEVRBalance();
```


## Get hook states - `async getHookStates()`
Gets XRPL all hook states in the registry account.

### Response format
Returns the list of hook states including evernode configurations and hosts. 
```
[
    {
        key: '4556520100000000000000000000000000000000000000000000000000000007',
        data: '54871AFD498D0000'
    },
    {
        key: '455652025372A50EC3F94F48AFD93548F10D33039A5A19FFD6BFD8090000006E',
        data: '7293E0D811B7065D3108FFB12E594A6881B32712496E74656C20436F72652050726F636573736F72202848617377656C6C2C206E6F205453582C20490004095F000C35000000234B00008E92'
    },
    ...
]
```
| Name | Type   | Description                         |
| ---- | ------ | ----------------------------------- |
| key  | string | Hex string hook state key buffer.   |
| data | string | Hex string of the hook data buffer. |

### Example
```javascript
    const states = await registryClient.getHookStates();
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
    const moment = await registryClient.getMoment();
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
    const startIdx = await registryClient.getMomentStartIndex();
```


## Refresh the evernode config - `async refreshConfig()`
Loads the configs from XRPL hook and updates the in memory config.

### Response format
This is a void function.

### Example
```javascript
    await registryClient.refreshConfig();
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
    const startIdx = await registryClient.extractEvernodeEvent(tx);
```


## Get host info - `async getHostInfo(hostAddress)`
Gets the registered host information.

### Parameters
| Name        | Type   | Description          |
| ----------- | ------ | -------------------- |
| hostAddress | string | Address of the host. |

### Response format
Returns the registered host information object. Returns null is not registered.
```
{
    address: 'r3tSGeDFJaz8GEVmM6oUuYTAiNdDJhitCt',
    nfTokenId: '000100005372A50EC3F94F48AFD93548F10D33039A5A19FFA8F436070000006C',
    countryCode: 'SG',
    description: '207.148.120.201',
    registrationLedger: 4924488,
    registrationFee: 5120,
    maxInstances: 6,
    activeInstances: 1,
    lastHeartbeatLedger: 6324852,
    version: '0.5.10',
    active: true,
    cpuModelName: 'AMD EPYC-Rome Processor',
    cpuCount: 2,
    cpuMHz: 1996,
    cpuMicrosec: 800000,
    ramMb: 9774,
    diskMb: 64120
}
```
| Name                | Type    | Description                                             |
| ------------------- | ------- | ------------------------------------------------------- |
| address             | string  | XRPL account address of the host.                       |
| nfTokenId           | string  | Registration NFT ID of the host                         |
| countryCode         | string  | Host machine's origin country code.                     |
| description         | string  | IP address or the DNS of the host.                      |
| registrationLedger  | number  | Host machine registered XRP ledger                      |
| registrationFee     | number  | Registration fee paid by the host when it's registered. |
| maxInstances        | number  | Max number of instances that can be created in the host |
| activeInstances     | number  | Currently allocated instance count in the host machine. |
| lastHeartbeatLedger | number  | XRP ledger that the last heartbeat is received          |
| version             | string  | Sashimono version installed in the host machine.        |
| active              | boolean | Boolean indicating whether the host is active or not.   |
| cpuModelName        | string  | CPU model of the host machine.                          |
| cpuCount            | number  | CPU count of the host machine                           |
| cpuMHz              | number  | CPU speed of the host.                                  |
| cpuMicrosec         | number  | CPU time in micro seconds allocated for evernode.       |
| ramMb               | number  | Host machine's evernode sllocated RAM in MBs            |
| diskMb              | number  | Disk space allocated for evernode in the host.          |

### Example
```javascript
    const hostInfo = await registryClient.getHostInfo('r3tSGeDFJaz8GEVmM6oUuYTAiNdDJhitCt');
```

## Get all evernode configurations - `async getAllHosts()`
Gets all the hosts without paginating.

### Response format
Returns the list of hosts.
```
[
    {
        id: '455652030000000000000000FB5BCF72851F76EF3031DBC95ECC6544090C96E8',
        createTime: 2022-07-05T12:22:15.505Z,
        updateTime: 2022-09-28T10:44:43.657Z,
        nfTokenId: '000800005372A50EC3F94F48AFD93548F10D33039A5A19FFFE25BCDE00000043',
        registrationLedger: 3904936,
        countryCode: 'US',
        cpuMHz: 2800,
        description: '107.174.93.135',
        registrationFee: 5120,
        lastHeartbeatLedger: 6324852,
        ramMb: 6576,
        cpuCount: 3,
        cpuModelName: 'Intel(R) Xeon(R) CPU E5-2680 v2 2.80GHz',
        diskMb: 44366,
        activeInstances: 1,
        cpuMicrosec: 800000,
        address: 'rPvhbE9hNgSCb6tgMCoDwsxRgewxcvD7jk',
        maxInstances: 6,
        key: '455652030000000000000000FB5BCF72851F76EF3031DBC95ECC6544090C96E8',
        version: '0.5.10'
    }
]
```
| Name                | Type     | Description                                                        |
| ------------------- | -------- | ------------------------------------------------------------------ |
| id                  | string   | Identifier of the host. This is used for internal use in evernode. |
| createTime          | datetime | Host record created date and time                                  |
| updateTime          | datetime | Host record updated date and time                                  |
| nfTokenId           | string   | Registration NFT ID of the host                                    |
| registrationLedger  | number   | Host machine registered XRP ledger                                 |
| countryCode         | string   | Host machine's origin country code.                                |
| cpuMHz              | number   | CPU speed of the host.                                             |
| description         | string   | IP address or the DNS of the host.                                 |
| registrationFee     | number   | Registration fee paid by the host when it's registered.            |
| lastHeartbeatLedger | number   | XRP ledger that the last heartbeat is received                     |
| ramMb               | number   | Host machine's evernode sllocated RAM in MBs                       |
| cpuCount            | number   | CPU count of the host machine                                      |
| cpuModelName        | string   | CPU model of the host machine.                                     |
| diskMb              | number   | Disk space allocated for evernode in the host.                     |
| activeInstances     | number   | Currently allocated instance count in the host machine.            |
| cpuMicrosec         | number   | CPU time in micro seconds allocated for evernode.                  |
| address             | string   | XRPL account address of the host.                                  |
| maxInstances        | number   | Max number of instances that can be created in the host            |
| key                 | string   | Same as the `id`. Used for internal use.                           |
| version             | string   | Sashimono version installed in the host machine.                   |

### Example
```javascript
    const hosts = await registryClient.getAllHosts();
```