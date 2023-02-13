# Evernode - Primer

## Apps vs DApps
We can think of an app as something which can process **Inputs** and produce **Outputs** while reading/updating **State**. Many traditional apps fall into this category and they can be implemented with many programming languages and platforms on the market.

<img width="227" alt="image" src="https://user-images.githubusercontent.com/33562092/218354996-3b7315e3-b0dc-449f-9e17-14a02d16f83c.png">

We can make a **decentralized** app (DApp) by making multiple instances of the app work together in unison to process outputs, state and produce outputs. There is no single (central) party to coordinate instances. All app instances are equal and performs the same job. The app instances verify with each other what they are processing is consistent and similar across all instances so they invidiaully produces the same result.

<img width="379" alt="image" src="https://user-images.githubusercontent.com/33562092/218356526-275fb1f6-1459-4412-9d38-1ec5931f10eb.png">

The process of communicating and verifying each other's behaviour and agreeing upon the correct results is called **Consensus**. There is no leader/coordinator to conduct this process. Every app instance must perform this activity invidividually. HotPocket provides this ability to your app in order to make it a "decentralized" app.

## HotPocket DApps

HotPocket is a consensus engine which helps decentralize POSIX-compliant apps written in traditional programming platforms. In HotPocket terminology, each application instance is a "**node**". Each node contains a copy of HotPocket software and your application. Your app contains the application logic and some integration points that can be used by HotPocket to control and communicate with it.

  - Users interacts with HotPocket in order to communicate with your app.

  - HotPocket takes care of the consensus process with other HotPocket nodes.

  - HotPocket controls following aspects of your app:
    - It handles user authentication.
    - It invokes your app periodically as governed by the consensus process.
    - It provides consensed user inputs to your app.
    - It monitors outputs and state modifications produced by your app.
    - It returns consensed outputs back to relevant users.
 
  - Your app is free to do anything a POSIX application software can do.
    - You can connect to internet and interact with other services.
    - You can use any conventional application frameworks/libraries in your app.
    - You can use the filesystem (or file-based databases) for local data persistence. HotPocket treats all filesystem operations performed by the app as "state".
    - You have full control over raw binary data of user Inputs and Outputs.
 
  - Your app needs to adhere to following:
    - Application logic needs to be **deterministic**. For a given **Input** and **State**, it must produce same **Output**. (eg. usage of machine timestamp or random numbers may break consensus)
    - It needs to be a Linux POSIX application.
    - It needs to expose the necessary HotPocket integration interfaces.