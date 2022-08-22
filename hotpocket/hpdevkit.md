# HotPocket developer kit
Evernode uses HotPocket as its smart contract engine. HotPocket smart contracts can be developed using any POSIX-compliant language/framework. To make it easy to develop and test HotPocket smart contracts on your local PC, you can use HotPocket developer kit.

## Installation

### Prerequisites
HotPocket developer kit requires you to install [Docker Engine](https://docs.docker.com/engine/install/) on your development machine.

### Windows installation
1. Download [hpdevkit.exe](https://stevernode.blob.core.windows.net/evernode-beta/hpdevkit-windows/hpdevkit.exe) CLI tool for Windows.
2. Place the `hpdevkit.exe` in a directory you want and add the directory to your PATH as follows
<img alt="Windows PATH environment variable setup" src="https://user-images.githubusercontent.com/33562092/174452298-4771127c-247b-4cf6-8bcc-3fff00af08e1.png">

### Linux installation
...in future...

## Creating HotPocket smart contract
You can use the HotPocket developer kit to generate smart contract projects so everything is pre-configured for you. Here, we are creating a NodeJs smart contract. This assumes you have prior experience with developing NodeJs applications.
```
hpdevkit gen nodejs starter-contract myproj
cd myproj
npm install
npm start
```
The above 'npm start' will build and deploy the nodejs smart contract into a single-node HotPocket cluster running in your machine. It will also start printing console output from the HotPocket instance. You can press ctrl+C to exit from monitoring HotPocket console output. HotPocket instance will continue to run even after you exit the monitoring console.

### Viewing logs
You can re-enter into the monitoring console with `hpdevkit logs 1` with `1` being the HotPocket instance number you want to monitor.

### Changing the cluster size
In order to resize the HotPocket cluster, you need to delete and create it again.
1. Delete the existing cluster: `hpdevkit clean`
2. Set HP_CLUSTER_SIZE environment variable to the size you want.
```
# Windows (command prompt)
set HP_CLUSTER_SIZE=3

# Windows (powershell)
$env:HP_CLUSTER_SIZE=3

# Linux/macOS (bash)
export HP_CLUSTER_SIZE=3
```
3. Deploy your contract again with `npm start`

## Creating HotPocket client application
...in future...

## Advanced usage
```
# Deploy contract files to single-node cluster.
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

Example `hp.cfg.override` for a nodejs application. (HotPocket devkit nodejs starter contract automatically includes this file for you)
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
| HP_CLUSTER_SIZE | Number of nodes in the cluster. Only becomes effective with fresh cluster. | `1` |
| HP_DEFAULT_NODE | The node the 'deploy' command uses to display logs. | `1` |
| HP_DEVKIT_IMAGE | Docker image to be used for devkit cluster management. | `evernodedev/hpdevkit` |
| HP_INSTANCE_IMAGE | Docker image to be used for HotPocket instances. | `evernodedev/hotpocket:latest-ubt.20.04-njs.16` |

## Updates
- Install latest `hpdevkit` as mentioned in install steps.
- Update the supporting docker image with `docker pull evernodedev/hpdevkit`

## Reporting issues
Report issues [here](https://github.com/HotPocketDev/evernode-sdk/issues).
