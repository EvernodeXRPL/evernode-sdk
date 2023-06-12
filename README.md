# Evernode SDK

The Evernode SDK consists of libraries and tools required to develop and deploy HotPocket smart contracts on Evernode. This repo is used to provide early access to libraries and tools that are work-in-progress and to get feedback.

Read [Evernode - Primer](primer.md) for an overview on Evernode.

## Tutorials

HotPocket is the smart contract execution and consensus engine of Evernode. HotPocket smart contracts can be developed using any POSIX-compliant language/framework. You can also develop client applications that interact with HotPocket smart contracts using web sockets. Please go through the following material to gain an understanding of HotPocket development and deployment to Evernode.

1. [HotPocket concepts](hotpocket/concepts.md)
2. [Basics tutorial](hotpocket/tutorial-basics.md)
3. [Working with multiple nodes](hotpocket/tutorial-multinode.md)
4. [Persisting data](hotpocket/tutorial-persistdata.md)
5. [Read requests](hotpocket/tutorial-readreq.md)
6. [NPL messaging](hotpocket/tutorial-npl.md)
7. [Instance sync](hotpocket/tutorial-instance-sync.md)
8. [Contract context](hotpocket/contract-context.md)
9. [Deploying contracts to Evernode - single instance](evernode/tutorial-basics.md)
10. [Deploying contracts to Evernode - cluster of instances](evernode/tutorial-cluster.md)

## Tools

- hpdevkit - You can use [HotPocket developer kit](hpdevkit/index.md) to get started easily and test HotPocket smart contracts on your local PC. Follow the [tutorials](#tutorials) above to understand how to use it.
- evdevkit - You can use [Evernode developer kit](evdevkit/index.md) to deploy your HotPocket smart contracts Evernode hosts. Follow the [evernode tutorial](evernode/tutorial-basics.md) to understand how to use it.

## Example code

- [Simple echo contract (nodejs)](https://github.com/HotPocketDev/hp-nodejs-contract/blob/main/example/echo-contract.js)
- [Example C contract](https://github.com/HotPocketDev/hp-c-contract/blob/main/example_contract.c)
- [Evernode bootstrap contract (C++)](https://github.com/HotPocketDev/evernode-bootstrap-contract)
- [Evernode javascript client usage examples](https://github.com/HotPocketDev/evernode-js-client/blob/main/test/test.js)
- [Example calculator contract and client (nodejs)](https://github.com/HotPocketDev/example-calculator-contract)

## Libraries

### HotPocket smart contract library

- [NodeJs library](https://www.npmjs.com/package/hotpocket-nodejs-contract) ([source](https://github.com/HotPocketDev/hp-nodejs-contract))
- [C library source](https://github.com/HotPocketDev/hp-c-contract)

### HotPocket client library

- [Javascript library](https://www.npmjs.com/package/hotpocket-js-client) for nodejs and browser. ([source](https://github.com/HotPocketDev/hp-js-client))

### Evernode client library

- [Javascript library](https://www.npmjs.com/package/evernode-js-client) for nodejs and browser. ([source](https://github.com/HotPocketDev/evernode-js-client))
  - [Common client functionality](evernode/reference-api-common.md)
  - [Hook Client Reference](evernode/reference-api-hook-clients.md)
  - [Tenant client](evernode/reference-api-tenant.md)
  - [Events](evernode/reference-api-events.md)

### Community libraries

- XRPL-based Decentralized Key Management framework for HotPocket: DKM - [intro](https://devpost.com/software/decentralized-key-management-evernode) | [source](https://github.com/wojake/DKM) (by [@wojake](https://github.com/wojake))

## Protocol reference

- [HotPocket Config reference](hotpocket/reference-configuration.md)
- [HotPocket Client protocol](hotpocket/reference-client-protocol.md)
- [HotPocket Contract protocol](hotpocket/reference-contract-protocol.md)

## Issues and feedback

Please post any issues and feedback you have [here](https://github.com/HotPocketDev/evernode-sdk/issues).

## Updates

[Subscribe here](https://github.com/HotPocketDev/evernode-sdk/issues/4) to receive updates about the Evernode SDK.
