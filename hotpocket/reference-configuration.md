# HotPocket configuration reference

## What is "hp.cfg" in HotPocket?

`hp.cfg` is a configuration file maintained inside each and every HotPocket node. It contains attributes necessary for smart contract execution.

Following is a sample `hp.cfg` file:

```json
{
    "node": {
        "role": "validator",
        "history": "custom",
        "history_config": {
            "max_primary_shards": 4,
            "max_raw_shards": 4
        }
    },
    "contract": {
        "execute": true,
        "log": {
            "enable": false,
            "max_mbytes_per_file": 5,
            "max_file_count": 10
        },
        "version": "1.0",
        "unl": [
            "edd35f188660785b631bd3a3cb00ac6d518aa71d85b69968dd85757fb97b28cb46",
            "edbdb14d3d56766dc73338d99da5f450952f6ab4763b612251e5761df11aa36a76"
            "ed9935dfcac9da0ffb499f02a4bd8143dcad6bf1948a02fde8ac58feb3d9c76a68"
        ],
        "bin_path": "/usr/bin/node",
        "bin_args": "index.js",
        "environment": {},
        "max_input_ledger_offset": 10,
        "consensus": {
            "mode": "public",
            "roundtime": 1000,
            "stage_slice": 25,
            "threshold": 80
        },
        "npl": {
            "mode": "public"
        },
        "round_limits": {
            "user_input_bytes": 0,
            "user_output_bytes": 0,
            "npl_output_bytes": 0,
            "proc_cpu_seconds": 0,
            "proc_mem_bytes": 0,
            "proc_ofd_count": 0
        }
    },
    "mesh": {
        "known_peers": [
            "172.1.1.2:22862",
            "172.1.1.3:22863"
        ],
        "msg_forwarding": true,
        "peer_discovery": {
            "enabled": true,
            "interval": 10000
        }
    },
    "log": {
        "log_level": "inf"
    }
}
```

## Attributes

In the above sample `hp.cfg` file, the attributes are grouped under the following sub-sections:

- ***node***
- ***contract***
- ***mesh***
- ***log***

The attributes of each sub-section are as follows:


- ### ***node***
    > This section contains attributes that are relevant to the node itself.

    | Field                | Description                                                                                                                                                                                                                                                                                                                                   |
    | -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | ***role***           | The role that is assigned to this particular node in the smart contract. The role can be either `validator` or `observer`.<br><br> **validator** - Participates in consensus by sending stage proposals to the connected peers.<br> **observer** - Does not participate in consensus and observes peer proposals and keeps itself up to date. |
    | ***history***        | The history mode of the node. There are two mode types: `full` and `custom`.<br><br> **full** - Keeps all the history without truncating.<br> **custom** - Truncates history according to the limits given in the `history_config` section.                                                                                                   |
    | ***history_config*** | ***max_primary_shards*** :  The maximum number of primary ledger shards that will be kept in history.<br> ***max_raw_shards*** : The maximum number of raw data shards that will be kept in history.                                                                                                                                          |

- ### ***contract***
  >  This section contains attributes that are required in smart contract maintenance. In a particular consensus round, this block will be synced with the other nodes which are participating in the consensus.

  | Field                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
  | ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
  | ***execute***                 | This denotes whether to execute the smart contract in the node or not.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
  | ***log***                     | ***enable*** :  `true` or `false` denoting whether to log stdout/err of the contract process as files inside log/contract directory or not.<br><br> ***max_mbytes_per_file*** : The maximum space allocation for a single log file.<br>  ***max_file_count***  : The maximum number of logs files, that can be maintained.                                                                                                                                                                                                                                                                                                                                                                                                              |
  | ***version***                 | The version of the contract in the cluster.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
  | ***unl***                     | The UNL (Unique Node List) is the list of the hex public keys of the trusted nodes that are participating for the consensus. Only the stage proposals sent by these trusted nodes are accepted.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
  | ***bin_path***                | The full path to the contract binary that is to be executed.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
  | ***bin_args***                | The CLI arguments to be passed into the smart contract.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
  | ***environment***             | The runtime environment variables to be used in the smart contract. This is specified as a JSON object. The values should be in string format. Example: `{ "VAR1": "value 1", "VAR2": "2" }`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
  | ***max_input_ledger_offset*** | The maximum ledger sequence number offset that can be specified in the input.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
  | ***consensus***               | ***mode*** :  This denotes whether the consensus mode is `private` or `public`. If set to `private` - stage proposals are sent only to the UNL nodes. If set to `public` - stage proposals are broadcasted to non-UNL nodes as well.<br> ***roundtime*** : The time duration between two consecutive consensus rounds. This value should be between 1 and 3600000 milliseconds inclusive.<br> ***stage_slice*** : The percentage slice of round time that stages 0,1,2 gets. This value should be between 1 and 33 inclusive.<br> ***threshold*** : The minimum percentage of votes for accepting a stage 3 proposal to create a ledger. This value can be between 1 and 100 inclusive. The default value for the cluster is set to 80. |
  | ***npl***                     | The NPL (Node Party Line) is a peer communication method used in HotPocket.  <br> ***mode***  : This can be used to configure the communication mode as `private` or `public`. Private and public in this instance behaves the same as the consensus.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
  | ***round_limits***            | ***user_input_bytes*** : The maximum number of input bytes that can be allocated for a consensus round. <br> ***user_output_bytes*** : The maximum number of output bytes that can be allocated for a consensus round.<br>  ***npl_output_bytes*** : The maximum number of NPL output bytes for a consensus round.    <br> ***proc_cpu_seconds***: The maximum amount of CPU time the contract process can consume. <br> ***proc_mem_bytes*** : The maximum amount of memory the contract process can allocate. <br> ***proc_ofd_count*** : The maximum number of open file descriptors the contract process can allocate.                                                                                                              |

- ### ***mesh***
    > This section contains attributes that are relevant to the peers in the node cluster.

    | Field                | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
    | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
    | ***known_peers***    | The list of known peers of this node. An element in this list should consist of the ip address and the port of the peer node in the format - `<ip_address>:<port>`.                                                                                                                                                                                                                                                                                                                  |
    | ***msg_forwarding*** | `true` or `false` denoting whether the message forward option is enabled or not. If enabled, this node will forward the received stage proposals and NPL messages to weakly connected peers (peers not connected with all the peers in the UNL). This will be helpful to continue communication between two nodes even when their direct connection is broken.                                                                                                                       |
    | ***peer_discovery*** | ***enabled*** : `true` or `false` denoting whether to enable or disable dynamic peer discovery. If the enabled node will request known peers from the other peers, then they will send the response with their known peer list. Then the node will update its known peer list upon receiving the response.<br> ***interval***: The time interval (in milliseconds) to send the peer list request. Dynamic peer discovery should be enabled for the time interval to have any effect. |  |


- ### ***log***
    > This section contains attributes that are relevant to the HotPocket logger.

    | Field           | Description                                          |
    | --------------- | ---------------------------------------------------- |
    | ***log_level*** | The log severity level. (`dbg`, `inf`, `wrn`, `err`) |


## ___NOTE:___
The smart contract '[mycontract](tutorial-basics.md/#create-the-smart-contract)' that you may have already created using the HotPocket Dev Kit contains a file called `hp.cfg.override`. This file can be used to bind attributes to the `hp.cfg` file that is used inside the node in your HotPocket cluster.