# Evernode defaults

## Override Evernode defaults - `static set(newDefaults)`
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

## Read Evernode defaults - `static get()`
Read Evernode default configs.

### Response format
The Object of Evernode configs

### Example
```javascript
    const defaults = Defaults.get();
```
<br>

# Evernode clients

There are three hook clients and a tenant client in the Evernode library.
- [Hook Clients](reference-api-hook-clients.md)
  - [Governor Client](reference-api-hook-clients.md#governor-client)
  - [Registry Client](reference-api-hook-clients.md#registry-client)
  - [Heartbeat Client](reference-api-hook-clients.md#heartbeat-client)
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

## Get hook states - `async getHookStates()`
Gets XRPL all hook states in the registry account.

### Response format
Returns the list of hook states including Evernode configuration and hosts.
```
[
    {
        key: '4556520100000000000000000000000000000000000000000000000000000008',
        data: '0A0014000000C04E00000000001800'
    },
    {
        key: '45565202E32B63CB70A23A5CB00E3CB58C8FAEF20F1FC0E81988ADD1286F254D',
        data: '2A42190773386D16A047F2A7433B0283303F1437496E74656C2852292058656F6E285229204350552045352D3236383020763220322E383047487A000200EF0A00350C0088130000409C000078727034457665727340676D61696C2E636F6D000000000000000000000000000000000000000000B1AADC421B001255'
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
    const states = await client.getHookStates();
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

## Get host info - `async getHostInfo(hostAddress = clientAddress)`
Gets the registered host information.

### Parameters
| Name                   | Type   | Description          |
| ---------------------- | ------ | -------------------- |
| hostAddress (optional) | string | Address of the host. |

### Response format
Returns the registered host information object. Returns null is not registered.
```
{
    address: 'rMJNjFm3qWMSpKfiN6REiTKWUK9aNBgDwM',
    uriTokenId: 'FCB22E2A22F2E284F3ADDE8ECFBCCB137B02761139734C8CE389ED9071E40230',
    countryCode: 'SG',
    description: '',
    registrationLedger: 1977027,
    registrationFee: 5120,
    maxInstances: 1,
    activeInstances: 0,
    lastHeartbeatIndex: 1686556202,
    version: '0.6.3',
    isATransferer: 0,
    lastVoteCandidateIdx: 2,
    lastVoteTimestamp: 1685940521,
    supportVoteSent: 1,
    registrationTimestamp: 1680843410,
    active: true,
    cpuModelName: 'Intel Core Processor (Broadwell, no TSX,',
    cpuCount: 1,
    cpuMHz: 2399,
    cpuMicrosec: 800000,
    ramMb: 2766,
    diskMb: 12368,
    email: 'testhost@gmail.com',
    accumulatedRewardAmount: '506.6666666666673'
}
```
| Name                    | Type    | Description                                                           |
| ----------------------- | ------- | --------------------------------------------------------------------- |
| address                 | string  | XRPL account address of the host.                                     |
| uriTokenId              | string  | Registration URI Token ID of the host.                                |
| countryCode             | string  | Host machine's origin country code.                                   |
| description             | string  | IP address or the DNS of the host.                                    |
| registrationLedger      | number  | Host machine registered XRP ledger.                                   |
| registrationFee         | number  | Registration fee paid by the host when it's registered.               |
| maxInstances            | number  | Max number of instances that can be created in the host.              |
| activeInstances         | number  | Currently allocated instance count in the host machine.               |
| lastHeartbeatIndex      | number  | Timestamp that the last heartbeat is received.                        |
| version                 | string  | Sashimono version installed in the host machine.                      |
| isATransferer           | number  | 1 - If transfer is initiated for the host, 0 - If not.                |
| lastVoteCandidateIdx    | number  | Index of the candidate which host has recently voted.                 |
| lastVoteTimestamp       | number  | Timestamp when the host sent the last vote.                           |
| supportVoteSent         | number  | 1 - If host sent a support vote for the moment, 0 - If not.           |
| registrationTimestamp   | number  | Timestamp when the host was registered.                               |
| active                  | boolean | Boolean indicating whether the host is active or not.                 |
| cpuModelName            | string  | CPU model of the host machine.                                        |
| cpuCount                | number  | CPU count of the host machine.                                        |
| cpuMHz                  | number  | CPU speed of the host.                                                |
| cpuMicrosec             | number  | CPU time in micro seconds allocated for Evernode.                     |
| ramMb                   | number  | Host machine's Evernode allocated RAM in MBs.                         |
| diskMb                  | number  | Disk space allocated for Evernode in the host.                        |
| email                   | number  | Public email address of the host.                                     |
| accumulatedRewardAmount | number  | Currently accumulated reward amount that foundation owed to the host. |

### Example
```javascript
    const hostInfo = await registryClient.getHostInfo('r3tSGeDFJaz8GEVmM6oUuYTAiNdDJhitCt');
```

<br>

## Get Evernode hosts - `async getHosts(filters = null, pageSize = null, nextPageToken = null)`
Get the hosts registered in Evernode. The result's are paginated. Default page size is 20. _Note: Specifying both filter and pagination does not supported._

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
            id: '45565203000000000000000026EDD6006A0B8E291F79977F15AE7B7DCDEE6356',
            createTime: 2023-06-04T15:31:46.159Z,
            updateTime: 2023-06-12T07:47:57.462Z,
            countryCode: 'GB',
            cpuCount: 2,
            isATransferer: 0,
            diskMb: 57080,
            supportVoteSent: 0,
            location: [Object],
            registrationTimestamp: 1685892692,
            registrationFee: 5120,
            maxInstances: 6,
            cpuMicrosec: 800000,
            address: 'rhYqbRQpSy7RtQtXjfurprdB4Gj8PAJW2X',
            ramMb: 6576,
            key: '45565203000000000000000026EDD6006A0B8E291F79977F15AE7B7DCDEE6356',
            version: '0.6.3',
            lastVoteTimestamp: 0,
            description: '',
            registrationLedger: 3653790,
            lastVoteCandidateIdx: 0,
            cpuModelName: 'AMD EPYC 7601 32-Core Processor',
            email: 'admin@healthdatainsights.co.uk',
            accumulatedRewardAmount: '506.6666666666673',
            uriTokenId: '9BE184D985503A95ED9D19FE412B3F53EECE279198DFBCFB49BC92EBC6356C8F',
            activeInstances: 0,
            cpuMHz: 2199,
            lastHeartbeatIndex: 1686556070,
            domain: 'evernode.healthdatainsights.co.uk',
            active: true
        },
        ...
    ],
    nextPageToken: 'AFTOeJxPpM1HDCTNN8QffkWo2-z57_I8o2B1vNKwf5HneSZd5HLuHlDKJ4ocdczkoXVhIeMJvgIlRqeDmbBmN8wKCf1sSArFD_tZz857UhVUfd-XvyIS19uINBJSBA7qUCIwFrwt5klXDQSsW2WKQk91yZwEEBKWl0yGeq3CSXndtDCmjHItMYKX6CSGqz2KLuRvPr6MBMHaisSg1vK-_q-fRqfzAty1S6EIgP-irbAU'
}
```
| Name                    | Type     | Description                                                           |
| ----------------------- | -------- | --------------------------------------------------------------------- |
| id                      | string   | Identifier of the host. This is used for internal use in Evernode.    |
| createTime              | datetime | Host record created date and time in the index db.                    |
| updateTime              | datetime | Host record updated date and time in the index db.                    |
| countryCode             | string   | Host machine's origin country code.                                   |
| cpuCount                | number   | CPU count of the host machine.                                        |
| isATransferer           | number   | 1 - If transfer is initiated for the host, 0 - If not.                |
| diskMb                  | number   | Disk space allocated for Evernode in the host.                        |
| supportVoteSent         | number   | 1 - If host sent a support vote for the moment, 0 - If not.           |
| location                | object   | Geographical data of the host location.                               |
| registrationTimestamp   | number   | Timestamp when the host was registered.                               |
| registrationFee         | number   | Registration fee paid by the host when it's registered.               |
| maxInstances            | number   | Max number of instances that can be created in the host.              |
| cpuMicrosec             | number   | CPU time in micro seconds allocated for Evernode.                     |
| address                 | string   | XRPL account address of the host.                                     |
| ramMb                   | number   | Host machine's Evernode allocated RAM in MBs.                         |
| key                     | string   | Same as the `id`. Used for internal use.                              |
| version                 | string   | Sashimono version installed in the host machine.                      |
| lastVoteTimestamp       | number   | Timestamp when the host sent the last vote.                           |
| description             | string   | IP address or the DNS of the host.                                    |
| registrationLedger      | number   | Host machine registered XRP ledger.                                   |
| lastVoteCandidateIdx    | number   | Index of the candidate which host has recently voted.                 |
| cpuModelName            | string   | CPU model of the host machine.                                        |
| email                   | number   | Public email address of the host.                                     |
| accumulatedRewardAmount | number   | Currently accumulated reward amount that foundation owed to the host. |
| uriTokenId              | string   | Registration URI Token ID of the host.                                |
| activeInstances         | number   | Currently allocated instance count in the host machine.               |
| cpuMHz                  | number   | CPU speed of the host.                                                |
| lastHeartbeatIndex      | number   | Timestamp that the last heartbeat is received.                        |
| domain                  | string   | Domain which the host is reachable.                                   |
| active                  | boolean  | Boolean indicating whether the host is active or not.                 |

### Example
```javascript
    const hosts = await registryClient.getHosts();
```

<br>

## Get candidates - `async getCandidates(filters = null, pageSize = null, nextPageToken = null)`
Get the candidates proposed in Evernode. The result's are paginated. Default page size is 20. _Note: Specifying both filter and pagination does not supported._

### Parameters
| Name                     | Type   | Description                                                                                          |
| ------------------------ | ------ | ---------------------------------------------------------------------------------------------------- |
| filters (optional)       | object | Filter criteria to filter the candidates. The filter key can be a either property of the candidate.. |
| pageSize (optional)      | number | Page size for the results.                                                                           |
| nextPageToken (optional) | string | Next page's token, If received by the previous result set.                                           |

### Response format
The list of candidates. The response will be in '{data: [], nextPageToken: ''}' only if there are more pages. Otherwise the response will only contain the host list. 
```
{
    data: [
        {
            id: '4556520601A4D4078AE696203161B568D06641F7C9299AEC447357A4253F8AAB',
            createTime: 2023-06-02T14:12:46.537Z,
            updateTime: 2023-06-02T14:12:46.537Z,
            createdTimestamp: 1685329691,
            index: 4,
            positiveVoteCount: 0,
            ownerAddress: 'rM5HSKpoCgJ1nbsNQCYvgUqXNnEXp5HsjW',
            foundationVoteStatus: 'rejected',
            lastVoteTimestamp: 0,
            uniqueId: '0000000001A4D4078AE696203161B568D06641F7C9299AEC447357A4253F8AAB',
            shortName: 'testProposal',
            registryHookHash: '1ADCDEF05B87BC22565647A2676B9961DD75C20212BE9E98E7A7EBC8A9BCF72B',
            status: 'rejected',
            heartbeatHookHash: '33BDC163ACFFE34EDE3C607DDF295B239A0A3C0506372072B370DB3786359938',
            proposalFee: '2560',
            governorHookHash: 'E2F7D833DF05EE69C6F4244F2E6C9B9D2745EAD1EC7503FA2B5102E24DA2C6F9',
            key: '4556520601A4D4078AE696203161B568D06641F7C9299AEC447357A4253F8AAB',
            statusChangeTimestamp: 1685329691
        },
        {
            id: '455652060300000000000000A9816179A627AA805A2393022766D5474CEFEED8',
            createTime: 2023-06-02T14:12:46.545Z,
            updateTime: 2023-06-02T14:12:46.545Z,
            statusChangeTimestamp: 1685329352,
            foundationVoteStatus: 'rejected',
            index: 3,
            shortName: 'dud_host',
            positiveVoteCount: 0,
            uniqueId: '000000000300000000000000A9816179A627AA805A2393022766D5474CEFEED8',
            lastVoteTimestamp: 0,
            dudHostAddress: 'rGTGBxN2ABeLjxveHXFCU5V8uqfoDEUJLB',
            createdTimestamp: 1685329352,
            status: 'rejected',
            proposalFee: '640',
            key: '455652060300000000000000A9816179A627AA805A2393022766D5474CEFEED8',
            ownerAddress: 'rhqHz5tuy3NnBTqcpsUVBXhSWCsDTCJmzE'
        },
        ...
    ],
    nextPageToken: 'AFTOeJxPpM1HDCTNN8QffkWo2-z57_I8o2B1vNKwf5HneSZd5HLuHlDKJ4ocdczkoXVhIeMJvgIlRqeDmbBmN8wKCf1sSArFD_tZz857UhVUfd-XvyIS19uINBJSBA7qUCIwFrwt5klXDQSsW2WKQk91yZwEEBKWl0yGeq3CSXndtDCmjHItMYKX6CSGqz2KLuRvPr6MBMHaisSg1vK-_q-fRqfzAty1S6EIgP-irbAU'
}
```
| Name                  | Type     | Description                                                              |
| --------------------- | -------- | ------------------------------------------------------------------------ |
| id                    | string   | Identifier of the candidate. This is used for internal use in Evernode.  |
| createTime            | datetime | Candidate record created date and time in the index db.                  |
| updateTime            | datetime | Candidate record updated date and time in the index db.                  |
| createdTimestamp      | number   | Timestamp when the candidate was created.                                |
| index                 | number   | Index of the candidate.                                                  |
| positiveVoteCount     | number   | Number of positive votes received to the candidate.                      |
| ownerAddress          | string   | XRPL address of the candidate owner.                                     |
| foundationVoteStatus  | string   | Vote given by the foundation.                                            |
| lastVoteTimestamp     | number   | Timestamp when the last was received for the candidate.                  |
| uniqueId              | string   | Unique identifier of the candidate.                                      |
| shortName             | string   | Short name for the candidate given by the owner.                         |
| status                | string   | Current election status of the candidate.                                |
| proposalFee           | string   | Amount of the proposal fee paid by the owner when creating the proposal. |
| key                   | string   | Same as the `id`. Used for internal use.                                 |
| statusChangeTimestamp | number   | Timestamp when the proposal election status is last changed.             |
_Following prpoerties are only shown in `New Hook` candidate type._
| governorHookHash      | string   | Proposed hook hash of the governor hook.                                 |
| registryHookHash      | string   | Proposed hook hash of the registry hook.                                 |
| heartbeatHookHash     | string   | Proposed hook hash of the heartbeat hook.                                |

### Example
```javascript
    const candidates = await registryClient.getCandidates();
```

<br>

## Get all Evernode configuration - `async getAllConfigs()`
Get all Evernode configuration without paginating.

### Response format
The list of configuration.
```
[
    {
        id: '4556520100000000000000000000000000000000000000000000000000000001',
        createTime: 2023-06-02T14:12:38.997Z,
        updateTime: 2023-06-02T14:12:38.997Z,
        value: 'ra328vuQhL5fKrjqGB3FzVM45a5zuNS2KR',
        type: 'configuration',
        key: '4556520100000000000000000000000000000000000000000000000000000001'
    },
    {
        id: '4556520100000000000000000000000000000000000000000000000000000008',
        createTime: 2023-06-02T14:12:39.000Z,
        updateTime: 2023-06-02T14:12:39.000Z,
        value: {
            epochCount: 10,
            epochRewardAmount: 5160960,
            rewardStartMoment: 0,
            accumulatedRewardFrequency: 24,
            firstEpochRewardQuota: 5120
        },
        type: 'configuration',
        key: '4556520100000000000000000000000000000000000000000000000000000008'
    },
    ...
]
```
| Name       | Type                   | Description                                                                 |
| ---------- | ---------------------- | --------------------------------------------------------------------------- |
| id         | string                 | Identifier of the configuration. This is used for internal use in Evernode. |
| createTime | datetime               | Candidate record created date and time in the index db.                     |
| updateTime | datetime               | Candidate record updated date and time in the index db.                     |
| type       | string                 | Configuration type.                                                         |
| value      | number, string, object | Configuration value.                                                        |

### Example
```javascript
    const configs = await registryClient.getAllConfigs();
```

<br>

## Get all Evernode hosts - `async getAllHosts()`
Gets all the hosts without paginating.

### Response format
Returns the list of hosts.
```
[
    {
        id: '45565203000000000000000026EDD6006A0B8E291F79977F15AE7B7DCDEE6356',
        createTime: 2023-06-04T15:31:46.159Z,
        updateTime: 2023-06-12T07:47:57.462Z,
        countryCode: 'GB',
        cpuCount: 2,
        isATransferer: 0,
        diskMb: 57080,
        supportVoteSent: 0,
        location: [Object],
        registrationTimestamp: 1685892692,
        registrationFee: 5120,
        maxInstances: 6,
        cpuMicrosec: 800000,
        address: 'rhYqbRQpSy7RtQtXjfurprdB4Gj8PAJW2X',
        ramMb: 6576,
        key: '45565203000000000000000026EDD6006A0B8E291F79977F15AE7B7DCDEE6356',
        version: '0.6.3',
        lastVoteTimestamp: 0,
        description: '',
        registrationLedger: 3653790,
        lastVoteCandidateIdx: 0,
        cpuModelName: 'AMD EPYC 7601 32-Core Processor',
        email: 'admin@healthdatainsights.co.uk',
        accumulatedRewardAmount: '506.6666666666673',
        uriTokenId: '9BE184D985503A95ED9D19FE412B3F53EECE279198DFBCFB49BC92EBC6356C8F',
        activeInstances: 0,
        cpuMHz: 2199,
        lastHeartbeatIndex: 1686556070,
        domain: 'evernode.healthdatainsights.co.uk',
        active: true
    },
    ...
]
```
| Name                    | Type     | Description                                                           |
| ----------------------- | -------- | --------------------------------------------------------------------- |
| id                      | string   | Identifier of the host. This is used for internal use in Evernode.    |
| createTime              | datetime | Host record created date and time in the index db.                    |
| updateTime              | datetime | Host record updated date and time in the index db.                    |
| countryCode             | string   | Host machine's origin country code.                                   |
| cpuCount                | number   | CPU count of the host machine.                                        |
| isATransferer           | number   | 1 - If transfer is initiated for the host, 0 - If not.                |
| diskMb                  | number   | Disk space allocated for Evernode in the host.                        |
| supportVoteSent         | number   | 1 - If host sent a support vote for the moment, 0 - If not.           |
| location                | object   | Geographical data of the host location.                               |
| registrationTimestamp   | number   | Timestamp when the host was registered.                               |
| registrationFee         | number   | Registration fee paid by the host when it's registered.               |
| maxInstances            | number   | Max number of instances that can be created in the host.              |
| cpuMicrosec             | number   | CPU time in micro seconds allocated for Evernode.                     |
| address                 | string   | XRPL account address of the host.                                     |
| ramMb                   | number   | Host machine's Evernode allocated RAM in MBs.                         |
| key                     | string   | Same as the `id`. Used for internal use.                              |
| version                 | string   | Sashimono version installed in the host machine.                      |
| lastVoteTimestamp       | number   | Timestamp when the host sent the last vote.                           |
| description             | string   | IP address or the DNS of the host.                                    |
| registrationLedger      | number   | Host machine registered XRP ledger.                                   |
| lastVoteCandidateIdx    | number   | Index of the candidate which host has recently voted.                 |
| cpuModelName            | string   | CPU model of the host machine.                                        |
| email                   | number   | Public email address of the host.                                     |
| accumulatedRewardAmount | number   | Currently accumulated reward amount that foundation owed to the host. |
| uriTokenId              | string   | Registration URI Token ID of the host.                                |
| activeInstances         | number   | Currently allocated instance count in the host machine.               |
| cpuMHz                  | number   | CPU speed of the host.                                                |
| lastHeartbeatIndex      | number   | Timestamp that the last heartbeat is received.                        |
| domain                  | string   | Domain which the host is reachable.                                   |
| active                  | boolean  | Boolean indicating whether the host is active or not.                 |

### Example
```javascript
    const hosts = await registryClient.getAllHosts();
```

<br>

## Get all candidates - `async getAllCandidates()`
Gets all the candidates without paginating.

### Response format
Returns the list of candidates.
```
[
    {
        id: '4556520601A4D4078AE696203161B568D06641F7C9299AEC447357A4253F8AAB',
        createTime: 2023-06-02T14:12:46.537Z,
        updateTime: 2023-06-02T14:12:46.537Z,
        createdTimestamp: 1685329691,
        index: 4,
        positiveVoteCount: 0,
        ownerAddress: 'rM5HSKpoCgJ1nbsNQCYvgUqXNnEXp5HsjW',
        foundationVoteStatus: 'rejected',
        lastVoteTimestamp: 0,
        uniqueId: '0000000001A4D4078AE696203161B568D06641F7C9299AEC447357A4253F8AAB',
        shortName: 'testProposal',
        registryHookHash: '1ADCDEF05B87BC22565647A2676B9961DD75C20212BE9E98E7A7EBC8A9BCF72B',
        status: 'rejected',
        heartbeatHookHash: '33BDC163ACFFE34EDE3C607DDF295B239A0A3C0506372072B370DB3786359938',
        proposalFee: '2560',
        governorHookHash: 'E2F7D833DF05EE69C6F4244F2E6C9B9D2745EAD1EC7503FA2B5102E24DA2C6F9',
        key: '4556520601A4D4078AE696203161B568D06641F7C9299AEC447357A4253F8AAB',
        statusChangeTimestamp: 1685329691
    },
    {
        id: '455652060300000000000000A9816179A627AA805A2393022766D5474CEFEED8',
        createTime: 2023-06-02T14:12:46.545Z,
        updateTime: 2023-06-02T14:12:46.545Z,
        statusChangeTimestamp: 1685329352,
        foundationVoteStatus: 'rejected',
        index: 3,
        shortName: 'dud_host',
        positiveVoteCount: 0,
        uniqueId: '000000000300000000000000A9816179A627AA805A2393022766D5474CEFEED8',
        lastVoteTimestamp: 0,
        dudHostAddress: 'rGTGBxN2ABeLjxveHXFCU5V8uqfoDEUJLB',
        createdTimestamp: 1685329352,
        status: 'rejected',
        proposalFee: '640',
        key: '455652060300000000000000A9816179A627AA805A2393022766D5474CEFEED8',
        ownerAddress: 'rhqHz5tuy3NnBTqcpsUVBXhSWCsDTCJmzE'
    },
    ...
]
```
| Name                  | Type     | Description                                                              |
| --------------------- | -------- | ------------------------------------------------------------------------ |
| id                    | string   | Identifier of the candidate. This is used for internal use in Evernode.  |
| createTime            | datetime | Candidate record created date and time in the index db.                  |
| updateTime            | datetime | Candidate record updated date and time in the index db.                  |
| createdTimestamp      | number   | Timestamp when the candidate was created.                                |
| index                 | number   | Index of the candidate.                                                  |
| positiveVoteCount     | number   | Number of positive votes received to the candidate.                      |
| ownerAddress          | string   | XRPL address of the candidate owner.                                     |
| foundationVoteStatus  | string   | Vote given by the foundation.                                            |
| lastVoteTimestamp     | number   | Timestamp when the last was received for the candidate.                  |
| uniqueId              | string   | Unique identifier of the candidate.                                      |
| shortName             | string   | Short name for the candidate given by the owner.                         |
| status                | string   | Current election status of the candidate.                                |
| proposalFee           | string   | Amount of the proposal fee paid by the owner when creating the proposal. |
| key                   | string   | Same as the `id`. Used for internal use.                                 |
| statusChangeTimestamp | number   | Timestamp when the proposal election status is last changed.             |
_Following properties are only shown in `New Hook` candidate type._
| governorHookHash      | string   | Proposed hook hash of the governor hook.                                 |
| registryHookHash      | string   | Proposed hook hash of the registry hook.                                 |
| heartbeatHookHash     | string   | Proposed hook hash of the heartbeat hook.                                |
_Following properties are only shown in `Dud Host` candidate type._
| dudHostAddress        | string   | Address of the dud host.                                                 |

### Example
```javascript
    const candidate = await registryClient.getAllCandidates();
```

<br>

## Prune dead host - `async pruneDeadHost(hostAddress)`
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

<br>

## Get owned new hook candidate - `async getCandidateByOwner(ownerAddress = clientAddress)`
Get proposed new hook candidate info.

### Parameters
| Name                    | Type   | Description           |
| ----------------------- | ------ | --------------------- |
| ownerAddress (optional) | string | Address of the owner. |

### Response format
The candidate information. Returns null if no candidate.
```
{
    ownerAddress: 'rM5HSKpoCgJ1nbsNQCYvgUqXNnEXp5HsjW',
    uniqueId: '0000000001A4D4078AE696203161B568D06641F7C9299AEC447357A4253F8AAB',
    governorHookHash: 'E2F7D833DF05EE69C6F4244F2E6C9B9D2745EAD1EC7503FA2B5102E24DA2C6F9',
    registryHookHash: '1ADCDEF05B87BC22565647A2676B9961DD75C20212BE9E98E7A7EBC8A9BCF72B',
    heartbeatHookHash: '33BDC163ACFFE34EDE3C607DDF295B239A0A3C0506372072B370DB3786359938',
    index: 4,
    shortName: 'testProposal',
    createdTimestamp: 1685329691,
    proposalFee: '2560',
    positiveVoteCount: 0,
    lastVoteTimestamp: 0,
    status: 'rejected',
    statusChangeTimestamp: 1685329691,
    foundationVoteStatus: 'rejected'
}
```
| Name                  | Type   | Description                                                              |
| --------------------- | ------ | ------------------------------------------------------------------------ |
| ownerAddress          | string | XRPL address of the candidate owner.                                     |
| uniqueId              | string | Unique identifier of the candidate.                                      |
| governorHookHash      | string | Proposed hook hash of the governor hook.                                 |
| registryHookHash      | string | Proposed hook hash of the registry hook.                                 |
| heartbeatHookHash     | string | Proposed hook hash of the heartbeat hook.                                |
| index                 | number | Index of the candidate.                                                  |
| shortName             | string | Short name for the candidate given by the owner.                         |
| id                    | string | Identifier of the candidate. This is used for internal use in Evernode.  |
| createdTimestamp      | number | Timestamp when the candidate was created.                                |
| proposalFee           | string | Amount of the proposal fee paid by the owner when creating the proposal. |
| positiveVoteCount     | number | Number of positive votes received to the candidate.                      |
| lastVoteTimestamp     | number | Timestamp when the last was received for the candidate.                  |
| status                | string | Current election status of the candidate.                                |
| statusChangeTimestamp | number | Timestamp when the proposal election status is last changed.             |
| foundationVoteStatus  | string | Vote given by the foundation.                                            |

### Example
```javascript
    const candidate = await client.getCandidateByOwner();
```

<br>

## Get owned dud host candidates - `async getDudHostCandidatesByOwner(ownerAddress = clientAddress)`
Get proposed dud host candidates.

### Parameters
| Name                    | Type   | Description           |
| ----------------------- | ------ | --------------------- |
| ownerAddress (optional) | string | Address of the owner. |

### Response format
An array of candidate information. Returns empty array if no candidates.
```
[
    {
        id: '455652060300000000000000A9816179A627AA805A2393022766D5474CEFEED8',
        createTime: 2023-06-02T14:12:46.545Z,
        updateTime: 2023-06-02T14:12:46.545Z,
        lastVoteTimestamp: 0,
        uniqueId: '000000000300000000000000A9816179A627AA805A2393022766D5474CEFEED8',
        dudHostAddress: 'rGTGBxN2ABeLjxveHXFCU5V8uqfoDEUJLB',
        index: 3,
        createdTimestamp: 1685329352,
        ownerAddress: 'rhqHz5tuy3NnBTqcpsUVBXhSWCsDTCJmzE',
        foundationVoteStatus: 'rejected',
        statusChangeTimestamp: 1685329352,
        positiveVoteCount: 0,
        proposalFee: '640',
        shortName: 'dud_host',
        key: '455652060300000000000000A9816179A627AA805A2393022766D5474CEFEED8',
        status: 'rejected'
    },
    ...
]
```
| Name                  | Type     | Description                                                              |
| --------------------- | -------- | ------------------------------------------------------------------------ |
| id                    | string   | Identifier of the candidate. This is used for internal use in Evernode.  |
| createTime            | datetime | Candidate record created date and time in the index db.                  |
| updateTime            | datetime | Candidate record updated date and time in the index db.                  |
| lastVoteTimestamp     | number   | Timestamp when the last was received for the candidate.                  |
| uniqueId              | string   | Unique identifier of the candidate.                                      |
| dudHostAddress        | string   | Address of the dud host.                                                 |
| index                 | number   | Index of the candidate.                                                  |
| createdTimestamp      | number   | Timestamp when the candidate was created.                                |
| ownerAddress          | string   | XRPL address of the candidate owner.                                     |
| foundationVoteStatus  | string   | Vote given by the foundation.                                            |
| statusChangeTimestamp | number   | Timestamp when the proposal election status is last changed.             |
| positiveVoteCount     | number   | Number of positive votes received to the candidate.                      |
| proposalFee           | string   | Amount of the proposal fee paid by the owner when creating the proposal. |
| shortName             | string   | Short name for the candidate given by the owner.                         |
| key                   | string   | Same as the `id`. Used for internal use.                                 |
| status                | string   | Current election status of the candidate.                                |

### Example
```javascript
    const candidates = await client.getDudHostCandidatesByOwner();
```

<br>

## Get candidate by Id - `async getCandidateById(candidateId)`
Get candidate info.

### Parameters
| Name        | Type   | Description          |
| ----------- | ------ | -------------------- |
| candidateId | string | Id of the candidate. |

### Response format
The candidate information. Returns null if no candidate.
```
{
  ownerAddress: 'rM5HSKpoCgJ1nbsNQCYvgUqXNnEXp5HsjW',
  uniqueId: '0000000001A4D4078AE696203161B568D06641F7C9299AEC447357A4253F8AAB',
  governorHookHash: 'E2F7D833DF05EE69C6F4244F2E6C9B9D2745EAD1EC7503FA2B5102E24DA2C6F9',
  registryHookHash: '1ADCDEF05B87BC22565647A2676B9961DD75C20212BE9E98E7A7EBC8A9BCF72B',
  heartbeatHookHash: '33BDC163ACFFE34EDE3C607DDF295B239A0A3C0506372072B370DB3786359938',
  index: 4,
  shortName: 'testProposal',
  createdTimestamp: 1685329691,
  proposalFee: '2560',
  positiveVoteCount: 0,
  lastVoteTimestamp: 0,
  status: 'rejected',
  statusChangeTimestamp: 1685329691,
  foundationVoteStatus: 'rejected'
}
```
| Name                  | Type   | Description                                                              |
| --------------------- | ------ | ------------------------------------------------------------------------ |
| ownerAddress          | string | XRPL address of the candidate owner.                                     |
| uniqueId              | string | Unique identifier of the candidate.                                      |
| index                 | number | Index of the candidate.                                                  |
| shortName             | string | Short name for the candidate given by the owner.                         |
| createdTimestamp      | number | Timestamp when the candidate was created.                                |
| proposalFee           | string | Amount of the proposal fee paid by the owner when creating the proposal. |
| positiveVoteCount     | number | Number of positive votes received to the candidate.                      |
| lastVoteTimestamp     | number | Timestamp when the last was received for the candidate.                  |
| status                | string | Current election status of the candidate.                                |
| statusChangeTimestamp | number | Timestamp when the proposal election status is last changed.             |
| foundationVoteStatus  | string | Vote given by the foundation.                                            |
_Following properties are only shown in `New Hook` candidate type._
| governorHookHash      | string | Proposed hook hash of the governor hook.                                 |
| registryHookHash      | string | Proposed hook hash of the registry hook.                                 |
| heartbeatHookHash     | string | Proposed hook hash of the heartbeat hook.                                |
_Following properties are only shown in `Dud Host` candidate type._
| dudHostAddress        | string | Address of the dud host.                                                 |

### Example
```javascript
    const candidate = await client.getCandidateById('0000000001A4D4078AE696203161B568D06641F7C9299AEC447357A4253F8AAB');
```

<br>

## Get dud host candidate info - `async getDudHostVoteInfo(hostAddress = clientAddress)`
Get reported dud host info.

### Parameters
| Name                   | Type   | Description              |
| ---------------------- | ------ | ------------------------ |
| hostAddress (optional) | string | Address of the dud host. |

### Response format
The dud host candidate information. Returns null if no candidate.
```
{
    ownerAddress: 'rhqHz5tuy3NnBTqcpsUVBXhSWCsDTCJmzE',
    index: 3,
    shortName: 'dud_host',
    createdTimestamp: 1685329352,
    proposalFee: '640',
    positiveVoteCount: 0,
    lastVoteTimestamp: 0,
    status: 'rejected',
    statusChangeTimestamp: 1685329352,
    foundationVoteStatus: 'rejected'
}
```
| Name                  | Type   | Description                                                              |
| --------------------- | ------ | ------------------------------------------------------------------------ |
| ownerAddress          | string | XRPL address of the candidate owner.                                     |
| index                 | number | Index of the candidate.                                                  |
| shortName             | string | Short name for the candidate given by the owner.                         |
| createdTimestamp      | number | Timestamp when the candidate was created.                                |
| proposalFee           | string | Amount of the proposal fee paid by the owner when creating the proposal. |
| positiveVoteCount     | number | Number of positive votes received to the candidate.                      |
| lastVoteTimestamp     | number | Timestamp when the last was received for the candidate.                  |
| status                | string | Current election status of the candidate.                                |
| statusChangeTimestamp | number | Timestamp when the proposal election status is last changed.             |
| foundationVoteStatus  | string | Vote given by the foundation.                                            |

### Example
```javascript
    const candidate = await client.getDudHostVoteInfo('rGTGBxN2ABeLjxveHXFCU5V8uqfoDEUJLB');
```

## Get piloted mode info - `async getPilotedModeVoteInfo()`
Get piloted mode vote info.

### Response format
The piloted mode candidate information. Returns null if no candidate.
```
{
    ownerAddress: 'rH2nhMjXt9tnFh3dyHqK4xS93LKRDMiYMK',
    index: 0,
    shortName: 'piloted_mode',
    createdTimestamp: 1686561512,
    proposalFee: '0',
    positiveVoteCount: 0,
    lastVoteTimestamp: 0,
    status: 'rejected',
    statusChangeTimestamp: 1686561512,
    foundationVoteStatus: 'rejected'
}
```
| Name                  | Type   | Description                                                              |
| --------------------- | ------ | ------------------------------------------------------------------------ |
| ownerAddress          | string | XRPL address of the candidate owner.                                     |
| index                 | number | Index of the candidate.                                                  |
| shortName             | string | Short name for the candidate given by the owner.                         |
| createdTimestamp      | number | Timestamp when the candidate was created.                                |
| proposalFee           | string | Amount of the proposal fee paid by the owner when creating the proposal. |
| positiveVoteCount     | number | Number of positive votes received to the candidate.                      |
| lastVoteTimestamp     | number | Timestamp when the last was received for the candidate.                  |
| status                | string | Current election status of the candidate.                                |
| statusChangeTimestamp | number | Timestamp when the proposal election status is last changed.             |
| foundationVoteStatus  | string | Vote given by the foundation.                                            |

### Example
```javascript
    const candidate = await client.getPilotedModeVoteInfo();
```