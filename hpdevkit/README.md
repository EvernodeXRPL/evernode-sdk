# Hot Pocket developer kit
Evernode uses Hot Pocket as its smart contract engine. Hot Pocket smart contracts can be developed using any POSIX-compliant language/framework. To make it easy to develop and test Hot Pocket smart contracts on your local PC, you can use Hot Pocket developer kit.

## Prerequisites
Hot Pocket developer kit requires you to install [Docker Engine](https://docs.docker.com/engine/install/) on your development machine.

## Windows installation
1. Download [hpdevkit.exe](https://github.com/HotPocketDev/evernode-sdk/releases/download/hpdevkit-win-0.1.0/hpdevkit.exe) CLI tool for Windows.
2. Place the `hpdevkit.exe` in a directory you want and add the directory to your PATH as follows
<img alt="Windows PATH environment variable setup" src="https://user-images.githubusercontent.com/33562092/174452298-4771127c-247b-4cf6-8bcc-3fff00af08e1.png">

## Linux and macOS installation
...in future...

## Creating Hot Pocket smart contract
You can use the Hot Pocket developer kit to generate smart contract projects so everything is pre-configured for you. Here, we are creating a NodeJs smart contract. This assumes you have prior experience with developing NodeJs applications.
```
hpdevkit gen nodejs starter myproj
cd myproj
npm install
npm start
```
The above 'npm start' will build and deploy the nodejs smart contract into a single-node Hot Pocket cluster running in your machine. It will also start printing console output from the Hot Pocket instance. You can press ctrl+C to exit from monitoring Hot Pocket console output. Hot Pocket instance will continue to run even after you exit the monitoring console.

### Viewing logs
You can re-enter into the monitoring console with `hpdevkit logs 1` with `1` being the Hot Pocket instance number you want to monitor.

### Changing the cluster size
In order to resize the Hot Pocket cluster, you need to delete and create it again.
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

## Creating Hot Pocket client application
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

Example `hp.cfg.override` for a nodejs application. (Hot Pocket devkit nodejs starter project automatically includes this file for you)
```
{
    "contract": {
        "bin_path": "/usr/bin/node",
        "bin_args": "app.js"
    }
}
```

## Environment variables
| Name | Description | Default value |
| --- | --- | --- |
| HP_CLUSTER_SIZE | Number of nodes in the cluster. Only becomes effective with fresh cluster. | `1` |
| HP_DEFAULT_NODE | The node the 'deploy' command uses to display logs. | `1` |
| HP_DEVKIT_IMAGE | Docker image to be used for devkit cluster management. | `evernodedev/hpdevkit` |
| HP_INSTANCE_IMAGE | Docker image to be used for Hot Pocket instances. | `evernodedev/hotpocket:latest-ubt.20.04-njs.16` |
