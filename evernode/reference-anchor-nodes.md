## Creating Anchor Nodes.

Once you deploy a contract to Evernode instance cluster, you do not have access to the data of the smart contract. Hence, if you face to a data recovery requirement there may be a hassle. Hence, Evernode foundation recommend you to use anchor nodes when deploying smart contracts.

Mainly there are two methods that you can adhere when creating an anchor node for your cluster.

### First Method : Create the anchor node when starting to create cluster.


There,you have to run your HotPocket contract on your machine.

Here we are using a Docker container to setup our anchor node.
Hence initially we need to create a separate volume for that Docker container.

You can define your own `Docker image` for this process with other additional command line tools for your ease.

```Dockerfile
# Dockerfile content
FROM evernodedev/hotpocket:0.6.0-ubt.20.04
RUN apt-get update
RUN apt-get -y install vim
```

Then you have to build your own docker image in order to use that one for the container preparation.
```docker
docker build -t evernode-anchor-node:0.1.0-hp.0.6.0 .
```


```docker
docker volume create hp_default_vol
```

Then we have to execute following command to create a HotPocket instance specifying its filesystem to the volume that we have created.

```docker
docker run --rm --mount type=volume,src=hp_default_vol,dst=/home/mnt --rm evernode-anchor-node:0.1.0-hp.0.6.0 new /home/mnt/contract

```
Then we need to create the Docker container by binding an external volume.

```docker
docker container create --name anchor_node --privileged -v ~/evernode/anchor/contract_data:/home/mnt/contract/contract_fs/seed/state -p 8080:8080 --restart unless-stopped --mount type=volume,src=hp_default_vol,dst=/home/mnt/ evernode-anchor-node:0.1.0-hp.0.6.0 run /home/mnt/contract
```

You can add the data and the contract build files to the `~/evernode/anchor/contract_data` once the container is started initially. Then those will be reflected in the state directory of the HotPocket contract side.

Once that is done, you can up the container by

```docker
# To find the Docker container id which was created recently.
docker ps -a

# To start the docker container.
docker start <container_id>
```

Then you need to modify hp.cfg file in your instance. Therefore you need to open up a bash session inside the container via using following command.

```docker
docker exec -it <container_id> /bin/bash
```
As I have created by docker image with vim tool. I can use that for my editing work.

```bash
root@950da071b9e1:/# vim /home/mnt/contract/cfg/hp.cfg
```
Here you need to specify the contract execution related attributes.
Like the binary path related to contract, arguments and environment variables in the `contract`section.

```json
"contract": {
    ...
    "bin_path": "/usr/bin/node",
    "bin_args": "index.js",
    "environment": {},
    ...
}
```

Furthermore you need to note down the contract ID and the public key of that node as we require those in the next Evernode instance acquisition to set in the instance configs.

Once you are done with these steps. You have to restart the docker container. You can check the logs using following docker command.

```docker
docker logs -f --tail=5 950da071b9e1
```

Now you can connect an acquired HotPocket instance with your local running node and expand the cluster.

The second node from Evernode should be acquired by specifying the anchor node's public key inside the UNL of instance configs in the acquisition. Also you have specify the relevant contract id, that you have noted down previously. Also, you have to specify the known-peer list as well in the acquisition.Now the acquired node starts to sync together with the anchor node.

Similarly you can introduces -number of instances to your cluster by following the above step repeatedly.

### Second Method : Join your own local smart contract to an existing cluster

For that also you need to create a HotPocket smart contract in your preferred path as in the above procedure. But you do not need to add any binaries or data files  because you are going to be synced with an existing cluster.

However, there you have to specify a peer with its peer IP address and the port number under the `known_peers` section in the hp.cfg. Also, you have to set the role of the node as an “observer” and set the `UNL` with a nodes' public keys that matches with your known peers. Initially, you have to configure somewhat higher `roundtime` in `hp.cfg` in the contract section, otherwise you will not be able to sync with the cluster.

Then you have to start the docker container. Then the nodes starts to connect with its peers and try to sync with the existing cluster.

After any of these approaches, you end up with a node that has your control. There you have access to the data of the smart contract. Then you can adhere to your own method of backup the state directory data files of the smart contract (…/contract_fs/seed/state).