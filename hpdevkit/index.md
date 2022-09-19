# HotPocket developer kit
Evernode uses HotPocket as its smart contract engine. HotPocket smart contracts can be developed using any POSIX-compliant language/framework. To make it easy to develop and test HotPocket smart contracts on your local PC, you can use HotPocket developer kit.

## Installation

### Prerequisites
HotPocket developer kit requires you to install [Docker Engine](https://docs.docker.com/engine/install/) and [NodeJs](https://nodejs.org/en/) on your development machine.

### Supports cross platform
This is a [npm global package](https://www.npmjs.com/package/hpdevkit) which supports both Linux and Windows
1. Install [prerequisites](#prerequisites).
2. Run the following command to install hpdevkit on your machine.
    ```
    npm i -g hpdevkit
    ```

_**NOTE:** In Linux platforms, for Installation you'll need root privileges. Add `sudo` to above command._

## Creating HotPocket smart contract
You can use the HotPocket developer kit to generate smart contract projects so everything is pre-configured for you. Here, we are creating a NodeJs smart contract. This assumes you have prior experience with developing NodeJs applications.
```
hpdevkit gen nodejs starter-contract myproj
cd myproj
npm install
npm start
```
The above 'npm start' will build and deploy the NodeJs smart contract into a HotPocket cluster running in your machine. It will also start printing console output from the HotPocket instance. You can press ctrl+C to exit from monitoring HotPocket console output. HotPocket instance will continue to run even after you exit the monitoring console.

### Viewing logs
You can re-enter into the monitoring console with `hpdevkit logs 1` with `1` being the HotPocket instance number you want to monitor.

### Changing the cluster size
In order to resize the HotPocket cluster, you need to delete and create it again.
1. Delete the existing cluster: `hpdevkit clean`
2. Set HP_CLUSTER_SIZE environment variable to the size you want.
    ```
    # Windows (command prompt)
    set HP_CLUSTER_SIZE=5

    # Windows (powershell)
    $env:HP_CLUSTER_SIZE=5

    # Linux (bash)
    export HP_CLUSTER_SIZE=5
    ```
3. Deploy your contract again with `npm start`

## Creating HotPocket client application
Using HotPocket developer kit you can generate a HotPocket client application so everything is pre-configured for you. Here, we are creating a NodeJs client. This assumes you have prior experience with developing NodeJs applications.
```
hpdevkit gen nodejs blank-client myclient
cd myclient
npm install
node myclient.js
```
You should have HotPocket nodes running on your machine for the client to connect. This will start the client and connect a HotPocket node listening to port 8081.

## Advanced usage
```
# Deploy contract files directory to the cluster (`npm start` internally uses this command to deploy).
hpdevkit deploy <contract files directory>

# Stop and cleanup everything (required for changing cluster size)
hpdevkit clean

# Look at specific node's logs.
hpdevkit logs <node number>

# Start/stop all nodes.
hpdevkit start
hpdevkit stop

# Start/stop specific node.
hpdevkit start <node number>
hpdevkit stop <node number>
```

If the contract files directory also contains a file named `hp.cfg.override`, it will be used to override the hp.cfg of all nodes. This can be used to set contract specific parameters like 'bin_path' and 'bin_args'

Example `hp.cfg.override` for a NodeJs application. (HotPocket devkit NodeJs starter contract automatically includes this file for you)
```
{
    "contract": {
        "bin_path": "/usr/bin/node",
        "bin_args": "app.js"
    }
}
```

## Environment variables
`hpdevkit` CLI supports following environment variables.

| Name | Description | Default value |
| --- | --- | --- |
| HP_CLUSTER_SIZE | Number of nodes in the cluster. Only becomes effective with fresh cluster. | `3` |
| HP_DEFAULT_NODE | The node the 'deploy' command uses to display logs. | `1` |
| HP_DEVKIT_IMAGE | Docker image to be used for devkit cluster management. | `evernodedev/hpdevkit` |
| HP_INSTANCE_IMAGE | Docker image to be used for HotPocket instances. | `evernodedev/hotpocket:latest-ubt.20.04-njs.16` |
| HP_USER_PORT_BEGIN | Starting user port number for the cluster. | `8081` |
| HP_PEER_PORT_BEGIN | Starting peer port number for the cluster. | `22861` |

## Updates
Update `hpdevkit` to the latest and update the supporting docker images.

Run one of following commands to update hpdevkit.
- Method 1 - Using hpdevkit CLI
    ```
    hpdevkit update
    ```

- Method 2 - Using npm
    ```
    npm update -g hpdevkit
    ```

**NOTE: You need to re-deploy your contracts to make the new changes effective.**

## Uninstall
Uninstall `hpdevkit` and the supporting docker images and containers.

- Using hpdevkit CLI
    ```
    hpdevkit uninstall
    ```

**NOTE: Uninstalling from hpdevkit CLI is recommended. If you uninstall using npm you'll have to clean hpdevkit supporting docker images and containers manually.**

_**NOTE:** In Linux platforms, for Update and Uninstallation you'll need root privileges. Add `sudo` to above commands._

## Reporting issues
Report issues [here](https://github.com/HotPocketDev/evernode-sdk/issues).
