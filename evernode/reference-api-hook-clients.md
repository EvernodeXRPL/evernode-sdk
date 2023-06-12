# Hook Client Reference

## Introduction

In `Evernode`, there are three distinct types of hooks:
- Governor Hook
- Registry Hook
- Heartbeat Hook.

Each of these hooks is associated with a separate XRPL account. Therefore, in various scenarios, it becomes necessary to create client instances to engage with these hooks.

This section aims to enhance your comprehension of the available hook clients in `Evernode`. It will provide you with detailed specifications and guidance on how to utilize them effectively within `Evernode`.

## Hook Client Factory Class

### Description
The Hook Client Factory class provides a common interface for creating hook clients. Developers can instantiate hook clients with minimal effort.

### Class Signature
```javascript
class HookClientFactory {
    static async create(hookType) {...}

    static async #getAccountAddress(hookType) {...}
}
```

### Create a particular Hook Client - `static async create(hookType)`

#### __Parameters :__

| Name     | Type   | Description                                                                              |
| -------- | ------ | ---------------------------------------------------------------------------------------- |
| hookType | string | Type of the Required Hook. (Supported Hook types 'GOVERNOR', 'REGISTRY' and 'HEARTBEAT') |

### Retrieve the address of a particular Hook Client - `static async #getAccountAddress(hookType)`

#### __Parameters :__
| Name     | Type   | Description                                                                              |
| -------- | ------ | ---------------------------------------------------------------------------------------- |
| hookType | string | Type of the Required Hook. (Supported Hook types 'GOVERNOR', 'REGISTRY' and 'HEARTBEAT') |

This method is invoked inside the `create` method in order to instantiate the corresponding client.

### Example
```javascript
const evernode = require("evernode-js-client");
...
const client = await evernode.HookClientFactory.create('REGISTRY');
```
<br>

## Governor Client

### Class Signature
```javascript
class GovernorClient extends BaseEvernodeClient {
    constructor(options = {}) {...}
}
```

### GovernorClient class constructor - `GovernorClient(options = {})`

#### __Parameters :__
Takes one parameter `options` which is a JSON object of options that is passed to GovernorClient.

```
{
    governorAddress: "rGVHr1PrfL93UAjyw3DWZoi9adz2sLp2yL",
}
```

| Name                       | Type   | Description                         |
| -------------------------- | ------ | ----------------------------------- |
| governorAddress (optional) | string | Governor Hook Account XRPL address. |

### Example
```javascript
const governorClient = new evernode.GovernorClient({
    governorAddress: "rGVHr1PrfL93UAjyw3DWZoi9adz2sLp2yL"
});
```
>__NOTE :__  If `governorAddress` is not specified, the GovernorClient class will default to using the governorAddress set in the `'Defaults'` configuration.

<br>

## Registry client

### Class Signature
```javascript
class RegistryClient extends BaseEvernodeClient {
    constructor(options = {}) {...}

    async getActiveHosts() {...}
}
```

### RegistryClient class constructor - `RegistryClient(options = {})`

#### __Parameters :__
Takes one parameter `options` which is a JSON object of options that is passed to RegistryClient.
```
{
    registryAddress: 'rQUhXd7sopuga3taru3jfvc1BgVbscrb1X',
    rippledServer: 'wss://hooks-testnet-v3.xrpl-labs.com'
}
```
| Name                     | Type   | Description                         |
| ------------------------ | ------ | ----------------------------------- |
| registryAddress          | string | Registry Hook Account XRPL address. |
| rippledServer (optional) | string | Rippled server URL.                 |

### Example
```javascript
const evernode = require("evernode-js-client");
...
const registryClient = new evernode.RegistryClient({
    registryAddress: 'rQUhXd7sopuga3taru3jfvc1BgVbscrb1X',
    rippledServer: 'wss://hooks-testnet-v3.xrpl-labs.com'
});
```

<br>

## Get active hosts - `async getActiveHosts()`
Gets all the active hosts registered in Evernode without paginating.

### Response format
Returns the list of active hosts.
```
[
  {
    id: '455652030000000000000000FB6ADC3C5C604E6926F99487882CF84C3C099723',
    createTime: 2023-04-13T23:03:57.013Z,
    updateTime: 2023-06-12T07:58:17.446Z,
    registrationTimestamp: 1681427022,
    ramMb: 6472,
    isATransferer: 0,
    activeInstances: 0,
    uriTokenId: '507B993418AD51FF668013A64FC2F57E5640D3FD8C57E88CB3DB44504FE8AC64',
    registrationLedger: 2170872,
    address: 'rPv4cAfWS9fguVuhQ4WMbQRa7FHsYWhdRc',
    version: '0.6.3',
    registrationFee: 5120,
    countryCode: 'US',
    cpuCount: 2,
    maxInstances: 6,
    lastVoteCandidateIdx: 0,
    description: '',
    cpuMicrosec: 800000,
    supportVoteSent: 0,
    lastHeartbeatIndex: 1686556680,
    cpuModelName: 'Intel(R) Xeon(R) CPU E5-2683 v3 2.00GHz',
    key: '455652030000000000000000FB6ADC3C5C604E6926F99487882CF84C3C099723',
    accumulatedRewardAmount: '506.6666666666673',
    cpuMHz: 1997,
    diskMb: 89198,
    email: 'deontik@mail.com',
    lastVoteTimestamp: 0,
    location: { latitude: '37.751', longitude: '-97.822', accuracyRadius: 1000 },
    domain: 'evernode.deontik.net',
    active: true
  },
    ...
]
```
| Name                    | Type     | Description                                                        |
| ----------------------- | -------- | ------------------------------------------------------------------ |
| id                      | string   | Identifier of the host. This is used for internal use in Evernode. |
| createTime              | datetime | Host record created date and time.                                 |
| updateTime              | datetime | Host record updated date and time.                                 |
| cpuCount                | number   | CPU count of the host machine.                                     |
| registrationTimestamp   | number   | Host machine registered timestamp.                                 |
| isTransferer            | number   | Whether this host is in a middle of a transfer.                    |
| activeInstances         | number   | Currently allocated instance count in the host machine.            |
| uriTokenId              | string   | Registration URI Token ID of the host.                             |
| registrationLedger      | number   | Host machine registered XRP ledger.                                |
| lastHeartbeatLedger     | number   | XRP ledger that the last heartbeat is received                     |
| address                 | string   | XRPL account address of the host.                                  |
| version                 | string   | Sashimono version installed in the host machine.                   |
| registrationFee         | number   | Registration fee paid by the host when it's registered.            |
| countryCode             | string   | Host machine's origin country code.                                |
| maxInstances            | number   | Max number of instances that can be created in the host .          |
| lastVoteCandidateIdx    | number   | Index of the last voted candidate.                                 |
| cpuCount                | number   | Number of cores in the host machine.                               |
| cpuMicrosec             | number   | CPU time in micro seconds allocated for Evernode.                  |
| cpuModelName            | string   | CPU model of the host machine.                                     |
| cpuMHz                  | number   | CPU speed of the host.                                             |
| diskMb                  | number   | Disk space allocated for Evernode in the host.                     |
| ramMb                   | number   | Host machine's Evernode allocated RAM in MBs.                      |
| accumulatedRewardAmount | number   | Accumulated reward amount since last rewarding.                    |
| email                   | string   | Contact Email address for the host.                                |
| location                | object   | IP address based locality of the host.                             |
| key                     | string   | Same as the `id`. Used for internal use.                           |
| domain                  | string   | The DNS of the host.                                               |
| active                  | boolean  | Boolean indicating whether the host is active or not.              |

### Example
```javascript
    const activeHosts = await registryClient.getActieHosts();
```
<br>

## Heartbeat Client

### Class Signature
```javascript
class HeartbeatClient extends BaseEvernodeClient {
    constructor(options = {}) {...}
}
```

### HeartbeatClient class constructor - `HeartbeatClient(options = {})`

#### __Parameters :__
Takes one parameter `options` which is a JSON object of options that is passed to HeartbeatClient.
```
{
    heartbeatAddress: 'raPSFU999HcwpyRojdNh2i96T22gY9fgxL',
    rippledServer: 'wss://hooks-testnet-v3.xrpl-labs.com'
}
```
| Name                     | Type   | Description                          |
| ------------------------ | ------ | ------------------------------------ |
| heartbeatAddress         | string | Heartbeat Hook Account XRPL address. |
| rippledServer (optional) | string | Rippled server URL.                  |

### Example
```javascript
const evernode = require("evernode-js-client");
...
const heartbeatClient = new evernode.HeartbeatClient({
    heartbeatAddress: 'raPSFU999HcwpyRojdNh2i96T22gY9fgxL',
    rippledServer: 'wss://hooks-testnet-v3.xrpl-labs.com'
});
```

<br>


### [Go to common api method references for hook clients.](reference-api-common.md)<br><br>


## Example usage above discussed Classes.
```javascript
const evernode = require("evernode-js-client");
const governorAddress = "rGVHr1PrfL93UAjyw3DWZoi9adz2sLp2yL"
const xrplApi = new evernode.XrplApi('wss://hooks-testnet-v3.xrpl-labs.com');

// Set the default values.
evernode.Defaults.set({
    governorAddress: governorAddress,
    xrplApi: xrplApi,
    networkID: 21338
});

async function test() {

    try {
        await xrplApi.connect();
        // Create Registry client using HookClientFactory.
        const registryClient = await evernode.HookClientFactory.create('REGISTRY');
        await registryClient.connect();

        // Retrieve the active hosts from registry client.
        const hosts = await registryClient.getActiveHosts();
        console.log("First 5 Active Hosts", hosts.slice(0, 5) || "No active hosts");
    } catch (e) {
        console.error("Error occured:", e);
    }
    finally {
        await xrplApi.disconnect();
    }
}

test();
```

