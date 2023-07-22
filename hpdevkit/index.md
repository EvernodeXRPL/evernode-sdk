# HotPocket developer kit

Evernode uses HotPocket as its smart contract engine. HotPocket smart contracts can be developed using any POSIX-compliant language/framework. Using the HotPocket developer kit, you can easily develop and test HotPocket smart contracts on your local PC.

## Installation

### Prerequisites

The HotPocket developer kit requires you to install [Docker Engine](https://docs.docker.com/engine/install/) and [NodeJs](https://nodejs.org/en/) on your development machine.

### Cross-platform support

The HotPocket developer kit is a [global npm package](https://www.npmjs.com/package/hpdevkit) which supports both Linux and Windows operating systems.

1. Install [prerequisites](#prerequisites).
2. Run the following command to install hpdevkit on your machine:
   ```
   npm i hpdevkit -g
   ```

_**NOTE:** In Linux platforms, installation requires root privileges. Hence, add `sudo` to the above command._

## Creating a HotPocket smart contract

You can use the HotPocket developer kit to generate smart contract projects such that everything is pre-configured. Here, we are creating a NodeJs smart contract (This assumes you have prior experience with developing NodeJs applications):

```
hpdevkit gen nodejs starter-contract myproj
cd myproj
npm install
npm start
```

The 'npm start' command will build and deploy the NodeJs smart contract into a HotPocket cluster running on your machine. It will also start printing the console output from the HotPocket instance. You can press Ctrl+C at any time to exit from monitoring the HotPocket console output. However, the HotPocket instance will continue to run even after you exit the monitoring console.

### Viewing logs

You can re-enter the monitoring console with the command `hpdevkit logs 1`, where `1` is the HotPocket instance number you wish to monitor.

### Changing the cluster size

In order to resize the HotPocket cluster, you need to delete and re-create it:

1. Delete the existing cluster using the `hpdevkit clean` command.
2. Set the HP_CLUSTER_SIZE environment variable to the size you want.

   ```
   # Windows (command prompt)
   set HP_CLUSTER_SIZE=5

   # Windows (powershell)
   $env:HP_CLUSTER_SIZE=5

   # Linux (bash)
   export HP_CLUSTER_SIZE=5
   ```

3. Deploy your contract again with `npm start`.

## Creating HotPocket client application

You can use the HotPocket developer kit to generate a HotPocket client application such that everything is pre-configured. Here, we are creating a NodeJs client (This assumes you have prior experience with developing NodeJs applications):

```
hpdevkit gen nodejs blank-client myclient
cd myclient
npm install
node myclient.js
```

This will start the client application and connect a HotPocket node listening to port 8081. Also note that you need to have HotPocket nodes running on your machine for the client to connect.

## Advanced usage

```
# Deploy contract files directory to the cluster (`npm start` internally uses this command to deploy)
hpdevkit deploy <contract files directory>

# Stop and clean-up everything (required for changing cluster size)
hpdevkit clean

# View a specific node's logs
hpdevkit logs <node number>

# Start/stop all nodes
hpdevkit start
hpdevkit stop

# Start/stop a specific node
hpdevkit start <node number>
hpdevkit stop <node number>

# Add a new node to the cluster
# (The new node will use the existing UNL and become an observer node)
hpdevkit spawn
```

If the contract files directory also contains a file named `hp.cfg.override`, it will be used to override the hp.cfg of all nodes. This can be used to set contract-specific parameters like 'bin_path' and 'bin_args'

An example `hp.cfg.override` file for a NodeJs application (the HotPocket developer kit NodeJs starter contract automatically includes this file for you):

```
{
    "contract": {
        "bin_path": "/usr/bin/node",
        "bin_args": "app.js"
    }
}
```

### Inspect contract files of all nodes

You can inspect the files of all the nodes of the cluster using Docker Desktop.
<img height="500" alt="image" src="https://user-images.githubusercontent.com/33562092/236630735-f39a3da0-78ca-4e91-9ef0-5c6400bd5bae.png" />

## Environment variables

`hpdevkit` CLI supports the following environment variables:

| Name               | Description                                                                      | Default value                                   |
| ------------------ | -------------------------------------------------------------------------------- | ----------------------------------------------- |
| HP_CLUSTER_SIZE    | The number of nodes in the cluster. This only takes effect with a fresh cluster. | `3`                                             |
| HP_DEFAULT_NODE    | The node which the 'deploy' command uses to display logs.                        | `1`                                             |
| HP_DEVKIT_IMAGE    | The Docker image to be used for devkit cluster management.                       | `evernodedev/hpdevkit`                          |
| HP_INSTANCE_IMAGE  | The Docker image to be used for HotPocket instances.                             | `evernodedev/hotpocket:latest-ubt.20.04-njs.20` |
| HP_USER_PORT_BEGIN | The starting user port number for the cluster.                                   | `8081`                                          |
| HP_PEER_PORT_BEGIN | The starting peer port number for the cluster.                                   | `22861`                                         |

## Updates

Run one of the following commands to update `hpdevkit` to the latest version, and update the supporting docker images:

- Using hpdevkit CLI

  ```
  hpdevkit update
  ```

- Using npm
  ```
  npm update hpdevkit -g
  ```

**NOTE: You need to re-deploy your contracts for the new changes to take effect.**

## Uninstall

Run the following command to uninstall `hpdevkit` and the supporting docker images and containers:

- Using hpdevkit CLI
  ```
  hpdevkit uninstall
  ```

**NOTE: Uninstalling from hpdevkit CLI is recommended. If you uninstall using npm, you will have to clean the hpdevkit supporting docker images and containers manually.**

_**NOTE:** In Linux platforms, you will need root privileges to execute updates and uninstallations. Hence, add `sudo` to the above commands._

## Reporting issues

Report issues [here](https://github.com/HotPocketDev/evernode-sdk/issues).
