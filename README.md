# Evernode SDK

The Evernode SDK consists of libraries and tools required to develop and deploy HotPocket smart contracts on Evernode. This repo is used to provide early access to libraries and tools that are work-in-progress and to get feedback.

## HotPocket development

HotPocket is the smart contract execution and consensus engine of Evernode. HotPocket smart contracts can be developed using any POSIX-compliant language/framework. You can also develop client applications that interact with HotPocket smart contracts using web sockets. Please go through the following material to gain an understanding of HotPocket development.

### Tutorials

1. [HotPocket concepts](hotpocket/concepts.md)
2. [Basics tutorial](hotpocket/tutorial-basics.md)
3. [Working with multiple nodes](hotpocket/tutorial-multinode.md)
4. [Persisting data](hotpocket/tutorial-persistdata.md)
5. [Read requests](hotpocket/tutorial-readreq.md)
6. [NPL messaging](hotpocket/tutorial-npl.md)

### HotPocket smart contract library

- [NodeJs library](https://www.npmjs.com/package/hotpocket-nodejs-contract) ([source](https://github.com/HotPocketDev/hp-nodejs-contract))
- [C library source](https://github.com/HotPocketDev/hp-c-contract)

### HotPocket client library

- [Javascript library](https://www.npmjs.com/package/hotpocket-js-client) for nodejs and browser. ([source](https://github.com/HotPocketDev/hp-js-client))

### Evernode client library

- [Javascript library](https://www.npmjs.com/package/evernode-js-client) for nodejs. ([source](https://github.com/HotPocketDev/evernode-js-client))

### HotPocket developer kit

You can use [HotPocket developer kit](hpdevkit/index.md) to get started easily and test HotPocket smart contracts on your local PC. Follow the [tutorials](#tutorials) above to understand how to use it.

### HotPocket references

Use these references to get a better understanding of HotPocket configuration and protocol.

- [Config reference](hotpocket/reference-configuration.md)
- [Client protocol reference](hotpocket/reference-client-protocol.md)
- [Contract protocol reference](hotpocket/reference-contract-protocol.md)

## Evernode integration

You need to interact with Evernode (via [XRPL](https://xrpl.org/)) to deploy your HotPocket smart contracts on the [Evernode network](https://dashboard.evernode.org). 

### Tutorials
1. [Basics tutorial](evernode/tutorial-basics.md)

### Evernode references

Use these references to get an idea about Evernode API methods.

- [Evernode clients common methods](evernode/reference-api-common.md)
- [Evernode registry client methods](evernode/reference-api-registry.md)
- [Evernode tenant client methods](evernode/reference-api-tenant.md)
- [Evernode client events](evernode/reference-api-events.md)

## Issues and feedback

Please post any issues and feedback you have [here](https://github.com/HotPocketDev/evernode-sdk/issues).

## Updates

[Subscribe here](https://github.com/HotPocketDev/evernode-sdk/issues/4) to receive updates about the Evernode SDK.