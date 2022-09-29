# Registry client

## RegistryClient class constructor - `RegistryClient(options = {})`

### Parameters
Takes one parameter `options` which is a JSON object of options that is passed to RegistryClient.
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
<br>

### [Go to common registry and tenant client api method references.](reference-api-common.md)<br><br>

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
| id                  | string   | Identifier of the host. This is used for internal use in Evernode. |
| createTime          | datetime | Host record created date and time                                  |
| updateTime          | datetime | Host record updated date and time                                  |
| cpuCount            | number   | CPU count of the host machine                                      |
| ramMb               | number   | Host machine's Evernode allocated RAM in MBs                       |
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
| cpuMicrosec         | number   | CPU time in micro seconds allocated for Evernode.                  |
| description         | string   | IP address or the DNS of the host.                                 |
| cpuMHz              | number   | CPU speed of the host.                                             |
| diskMb              | number   | Disk space allocated for Evernode in the host.                     |
| key                 | string   | Same as the `id`. Used for internal use.                           |
| active              | boolean  | Boolean indicating whether the host is active or not.              |

### Example
```javascript
    const activeHosts = await registryClient.getActiveHosts();
```


## Get all hosts - `async getHosts()`
Gets all the hosts registered in Evernode in paginated manor. The result's are paginated. Default page size is 20.
_Note: Specifying both filter and pagination does not supported. _

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
| id                  | string   | Identifier of the host. This is used for internal use in Evernode. |
| createTime          | datetime | Host record created date and time                                  |
| updateTime          | datetime | Host record updated date and time                                  |
| cpuCount            | number   | CPU count of the host machine                                      |
| ramMb               | number   | Host machine's Evernode allocated RAM in MBs                       |
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
| cpuMicrosec         | number   | CPU time in micro seconds allocated for Evernode.                  |
| description         | string   | IP address or the DNS of the host.                                 |
| cpuMHz              | number   | CPU speed of the host.                                             |
| diskMb              | number   | Disk space allocated for Evernode in the host.                     |
| key                 | string   | Same as the `id`. Used for internal use.                           |
| active              | boolean  | Boolean indicating whether the host is active or not.              |
| nextPageToken       | string   | Token reference for the next page.                                 |

### Example
```javascript
    const activeHosts = await registryClient.getHosts();
```


## Get hook states - `async getHookStates()`
Gets XRPL all hook states in the registry account.

### Response format
Returns the list of hook states including Evernode configurations and hosts. 
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
| cpuMicrosec         | number  | CPU time in micro seconds allocated for Evernode.       |
| ramMb               | number  | Host machine's Evernode sllocated RAM in MBs            |
| diskMb              | number  | Disk space allocated for Evernode in the host.          |

### Example
```javascript
    const hostInfo = await registryClient.getHostInfo('r3tSGeDFJaz8GEVmM6oUuYTAiNdDJhitCt');
```

## Get all Evernode configurations - `async getAllHosts()`
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
| id                  | string   | Identifier of the host. This is used for internal use in Evernode. |
| createTime          | datetime | Host record created date and time                                  |
| updateTime          | datetime | Host record updated date and time                                  |
| nfTokenId           | string   | Registration NFT ID of the host                                    |
| registrationLedger  | number   | Host machine registered XRP ledger                                 |
| countryCode         | string   | Host machine's origin country code.                                |
| cpuMHz              | number   | CPU speed of the host.                                             |
| description         | string   | IP address or the DNS of the host.                                 |
| registrationFee     | number   | Registration fee paid by the host when it's registered.            |
| lastHeartbeatLedger | number   | XRP ledger that the last heartbeat is received                     |
| ramMb               | number   | Host machine's Evernode allocated RAM in MBs                       |
| cpuCount            | number   | CPU count of the host machine                                      |
| cpuModelName        | string   | CPU model of the host machine.                                     |
| diskMb              | number   | Disk space allocated for Evernode in the host.                     |
| activeInstances     | number   | Currently allocated instance count in the host machine.            |
| cpuMicrosec         | number   | CPU time in micro seconds allocated for Evernode.                  |
| address             | string   | XRPL account address of the host.                                  |
| maxInstances        | number   | Max number of instances that can be created in the host            |
| key                 | string   | Same as the `id`. Used for internal use.                           |
| version             | string   | Sashimono version installed in the host machine.                   |

### Example
```javascript
    const hosts = await registryClient.getAllHosts();
```