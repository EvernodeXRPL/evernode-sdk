# Evernode SDK

Evernode SDK consists of libraries and tools required to develop and deploy HotPocket smart contracts on Evernode. This repo is used to provide early access to libraries and tools that are work-in-progress and get feedback.

We do not have any documentation published at this stage. Please let the library source code and readme pages guide you.

## HotPocket smart contract development

Evernode uses HotPocket as its smart contract engine. HotPocket smart contracts can be developed using any POSIX-compliant language/framework. Currently we have made following libraries available to make it easier.

- [NodeJs library](https://www.npmjs.com/package/hotpocket-nodejs-contract) ([source](https://github.com/HotPocketDev/hp-nodejs-contract))
- [C library source](https://github.com/HotPocketDev/hp-c-contract)


## HotPocket client development

Client applications can interact with HotPocket smart contracts via web sockets. We provide following client libraries to make it easier.

- [Javascript library](https://www.npmjs.com/package/hotpocket-js-client) for nodejs and browser. ([source](https://github.com/HotPocketDev/hp-js-client))


## HotPocket developer kit

To make it easy to get started and test HotPocket smart contracts on your local PC, you can use [HotPocket developer kit](hpdevkit).

## Evernode interactions

You need to interact with Evernode (via XRPL) to deploy your contracts on Evernode network. We will be introducing the necessary tools and libraries for that in the future.
