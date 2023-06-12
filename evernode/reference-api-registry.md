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
<br>

## Get Evernode hosts - `async getAllHosts()`
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
<br>

## Get Evernode configs - `async getAllConfigs()`
Gets all the configuration values without paginating.

### Response format
Returns the list of config values.
```
[
    {
        id: '4556520100000000000000000000000000000000000000000000000000000007',
        createTime: 2022-06-24T10:08:52.756Z,
        updateTime: 2022-06-24T10:08:52.756Z,
        value: '2',
        key: '4556520100000000000000000000000000000000000000000000000000000007',
        type: 'configuration'
    },
    {
        id: '4556523400000000000000000000000000000000000000000000000000000000',
        createTime: 2022-06-24T10:08:51.624Z,
        updateTime: 2022-06-24T10:08:51.624Z,
        key: '4556523400000000000000000000000000000000000000000000000000000000',
        type: 'singleton',
        value: 5120
    },
    ...
]
```
| Name       | Type     | Description                                                                         |
| ---------- | -------- | ----------------------------------------------------------------------------------- |
| id         | string   | Identifier of the config. This is used for internal use in Evernode.                |
| createTime | datetime | Config record created date and time                                                 |
| updateTime | datetime | Config record updated date and time                                                 |
| type       | string   | Configuration type (`configuration`,`singleton`)                                    |
| key        | string   | Same as the `id`. Used for internal use.                                            |
| value      | any      | Configuration value. This can be an any type according to the configuration format. |

### Example
```javascript
    const configs = await registryClient.getAllConfigs();
```
