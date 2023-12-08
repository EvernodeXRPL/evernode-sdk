# Evernode - Primer

## Apps vs dApps

We can think of an app as something which can process **Inputs** and produce **Outputs** while reading/updating **State**. Many traditional apps fall into this category and they can be implemented with many programming languages and platforms on the market.

<img width="227" alt="image" src="https://user-images.githubusercontent.com/33562092/218354996-3b7315e3-b0dc-449f-9e17-14a02d16f83c.png">

We can make a **decentralized** app (dApp a.k.a smart contract) by making multiple instances of the app work together in unison to process inputs, state and produce outputs. There is no single (central) party to coordinate instances. All app instances are equal and perform the same job. The app instances verify with each other that what they are processing is consistent and similar across all instances so they individually produce the same result.

<img width="379" alt="image" src="https://user-images.githubusercontent.com/33562092/218356526-275fb1f6-1459-4412-9d38-1ec5931f10eb.png">

The process of communicating and verifying each other's behaviour and agreeing upon the correct results is called **Consensus**. There is no leader/coordinator to conduct this process. Every app instance must perform this activity invidividually. HotPocket provides this ability to your app in order to make it a "decentralized" app.

## HotPocket Consensus Engine - for decentralized apps

HotPocket Consensus Engine is a consensus engine which helps decentralize POSIX-compliant apps written in traditional programming platforms. In HotPocket terminology, each application instance is a "**node**". Each node contains a copy of the HotPocket software and your application. Your app contains the application logic and some integration points that can be used by HotPocket to control and communicate with it. See [tutorials](https://github.com/EvernodeXRPL/evernode-sdk#tutorials) on how to create HotPocket dApps.

- Users interacts with HotPocket via websockets in order to communicate with your app.

- HotPocket takes care of the consensus process with other HotPocket nodes via websockets.

- HotPocket controls the following aspects of your app:

  - It invokes your app periodically as governed by the consensus process.
  - It provides consensed user inputs to your app.
  - It monitors outputs and state modifications produced by your app.
  - It returns consensed outputs back to relevant users.
  - It maintains a blockchain ledger to record the behaviour of your app over time.

- Your app is free to do anything a POSIX application software can do.

  - You can connect to internet and interact with other web services.
  - You can use any conventional application frameworks/libraries in your app.
  - You can use the filesystem (or file-based databases) for data persistence. HotPocket treats the filesystem as "state".
  - You have full control over raw binary data of user Inputs and Outputs.

- Your app needs to adhere to following:
  - Application logic needs to be **deterministic**. For a given **Input** and **State**, it must produce the same **Output**. (eg. usage of machine timestamp or random numbers may break consensus)
  - It needs to be a Linux POSIX-compliant application.
  - It needs to expose the necessary HotPocket integration interfaces.

<img width="421" alt="image" src="https://user-images.githubusercontent.com/33562092/218616038-001a3b46-4aed-4a3e-b410-ee0b2e8930b4.png">

## Evernode - for decentralized infrastructure

HotPocket enables you to create decentralized apps. In order to run them you need multiple machines/hardware to host your dApp nodes. Ideally, dApp infrastructure also needs to be decentralized so the dApp nodes are not controlled/owned by a single party. Evernode provides the **decentralized infrastructure** required for this. It is a [decentralized marketplace](https://dashboard.evernode.org) in which hosting resources can be provisioned and used by your HotPocket dApps.

Evernode uses [XRPL hooks](https://hooks.xrpl.org/)-enabled network to coordinate its decentralized marketplace activities. **Hosts** (hosting hardware) can register themselves on the marketplace. **Tenants** can lease dApp hosting resources from any of the hosts using **Evers** (Evernode's native currency - EVR). Each leased hosting instance is a HotPocket node. The tenant deploys their dApp files into the HotPocket node. Leasing and deploying into multiple such HotPocket nodes creates a dApp cluster.

- Evernode **Hosts** and **Tenants** are represented by XRPL accounts.
- Marketplace information and rules are goverened by [XRPL hooks](https://hooks.xrpl.org/).
- Marketplace operations (host registration, leasing etc...) are represented by XRPL transactions.
- A single host can make many dApp nodes available for lease using its hardware resources.
- dApp operations (deployment, consensus, user interaction) happen directly between HotPocket nodes and users via websockets. They are NOT dependent on the XRPL network. They simply use the network "ports" allocated by the Host (Read [more](https://github.com/EvernodeXRPL/evernode-host#firewalls-and-ports)).

The following diagram shows how different HotPocket dApps would be distributed across many Evernode hosts. Each dApp forms its own mesh network.

<img width="436" alt="image" src="https://user-images.githubusercontent.com/33562092/218644541-ff77330f-67cf-4962-96f9-e35c6d3e1c33.png">

See [here](https://github.com/EvernodeXRPL/evernode-host) on how to become an Evernode Host.

## Further reading

- HotPocket - [concepts](hotpocket/concepts.md) | [tutorial](hotpocket/tutorial-basics.md)
- Sashimono - [overview](https://github.com/EvernodeXRPL/evernode-host/blob/main/sashimono.md) | [architecture](http://blog.geveo.com/Sashimono-Designing-a-multi-tenant-dApp-hosting-platform)
- [XRPL hooks](https://hooks.xrpl.org/)
