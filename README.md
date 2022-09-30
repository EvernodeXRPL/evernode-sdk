# Evernode SDK

The Evernode SDK consists of libraries and tools required to develop and deploy HotPocket smart contracts on Evernode. This repo is used to provide early access to libraries and tools that are work-in-progress and to get feedback.

## Tutorials

HotPocket is the smart contract execution and consensus engine of Evernode. HotPocket smart contracts can be developed using any POSIX-compliant language/framework. You can also develop client applications that interact with HotPocket smart contracts using web sockets. Please go through the following material to gain an understanding of HotPocket development and deployment to Evernode.

1. [HotPocket concepts](hotpocket/concepts.md)
2. [Basics tutorial](hotpocket/tutorial-basics.md)
3. [Working with multiple nodes](hotpocket/tutorial-multinode.md)
4. [Persisting data](hotpocket/tutorial-persistdata.md)
5. [Read requests](hotpocket/tutorial-readreq.md)
6. [NPL messaging](hotpocket/tutorial-npl.md)
7. [Deploying to Evernode](evernode/tutorial-basics.md)

## Tools

You can use [HotPocket developer kit](hpdevkit/index.md) to get started easily and test HotPocket smart contracts on your local PC. Follow the [tutorials](#tutorials) above to understand how to use it.

## Libraries

### HotPocket smart contract library

- [NodeJs library](https://www.npmjs.com/package/hotpocket-nodejs-contract) ([source](https://github.com/HotPocketDev/hp-nodejs-contract))
- [C library source](https://github.com/HotPocketDev/hp-c-contract)

### HotPocket client library

- [Javascript library](https://www.npmjs.com/package/hotpocket-js-client) for nodejs and browser. ([source](https://github.com/HotPocketDev/hp-js-client))

### Evernode client library

- [Javascript library](https://www.npmjs.com/package/evernode-js-client) for nodejs and browser. ([source](https://github.com/HotPocketDev/evernode-js-client))
  - [Common client functionality](evernode/reference-api-common.md)
  - [Registry client](evernode/reference-api-registry.md)
  - [Tenant client](evernode/reference-api-tenant.md)
  - [Events](evernode/reference-api-events.md)

## Protocol reference

- [HotPocket Config reference](hotpocket/reference-configuration.md)
- [HotPocket Client protocol](hotpocket/reference-client-protocol.md)
- [HotPocket Contract protocol](hotpocket/reference-contract-protocol.md)

## Issues and feedback

Please post any issues and feedback you have [here](https://github.com/HotPocketDev/evernode-sdk/issues).

## Updates

[Subscribe here](https://github.com/HotPocketDev/evernode-sdk/issues/4) to receive updates about the Evernode SDK.