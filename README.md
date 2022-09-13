# Evernode SDK

Evernode SDK consists of libraries and tools required to develop and deploy HotPocket smart contracts on Evernode. This repo is used to provide early access to libraries and tools that are work-in-progress and get feedback.

## HotPocket development

HotPocket is the smart contract execution and consensus engine of Evernode. HotPocket smart contracts can be developed using any POSIX-compliant language/framework. You can also develop client applications that interact with HotPocket smart contracts using web sockets. Please go through following material to gain an understanding of HotPocket development.

### Tutorials

1. [HotPocket concepts](hotpocket/concepts.md)
2. [Basics tutorial](hotpocket/tutorial-basics.md)
3. [Working with multiple nodes](hotpocket/tutorial-multinode.md)
4. [Persisting data](hotpocket/tutorial-persistdata.md)
5. [Read requests](hotpocket/tutorial-readreq.md)

### HotPocket smart contract library

- [NodeJs library](https://www.npmjs.com/package/hotpocket-nodejs-contract) ([source](https://github.com/HotPocketDev/hp-nodejs-contract))
- [C library source](https://github.com/HotPocketDev/hp-c-contract)


### HotPocket client library

- [Javascript library](https://www.npmjs.com/package/hotpocket-js-client) for nodejs and browser. ([source](https://github.com/HotPocketDev/hp-js-client))


### HotPocket developer kit

To make it easy to get started and test HotPocket smart contracts on your local PC, you can use [HotPocket developer kit](hpdevkit/index.md). Follow the [tutorials](#tutorials) above to know how to use it.

### HotPocket references

To obtain a better understanding of HotPocket configurations and protocols.

- [Config reference](hotpocket/reference-configurations.md)


## Evernode integration

You need to interact with Evernode (via [XRPL](https://xrpl.org/)) to deploy your HotPocket smart contracts on [Evernode network](https://dashboard.evernode.org). We will be introducing the necessary tools and libraries for that in the future.

## Issues and feedback

Please post any issues and feedback you have [here](https://github.com/HotPocketDev/evernode-sdk/issues).

## Updates

[Subscribe here](https://github.com/HotPocketDev/evernode-sdk/issues/4) to receive updates about Evernode SDK.