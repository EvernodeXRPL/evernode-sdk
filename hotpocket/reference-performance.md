## HotPocket Contract Performance.

### Client Side Performance Checks

When you are implementing HotPocket Smart Contracts, you may have to implement a client application for that smart contract in order to communicate with that smart contract. Also, sometimes you may need that to monitor your smart contract as well. If you want to analyse the performance of your smart contract, you can implement a client application with the ability to do certain computations regarding to the smart contract performance.

As you know HotPocket supports two types of requests such as,
    - Contract Input Requests
    - Contract Read Requests (No contract invocation happens)

Hence you can define your own performance criteria based on the above requests. For an example you can check the time duration taken for following activities,
    - Single contract input submission.
    - Multiple contract input submission,
    - Single read request submission.
    - Multiple read request submission.
    - Larger payload submission.

You can start a time when submitting a request and stop the timer when you receive a response form HotPocket smart contract. Likewise you can define your own criteria in your client application. Based on the results you can further refactor your smart contract and in order to improve the performance as well.

### HotPocket Contract Health Notification

This notification is sent to users from HotPocket when the health status is changed. Users can subscribe to this notification by sending a subscription request with the 'health_event' channel.

This event is helpful when you want to monitor the condition of proposals and the connectivity. Once you tune up your client with that `health_event` you can acquire certain information using below message content. You can maintain a separate log file in order to track the data for further analysis.
```
{
    "type": "health_event",
    "event": "proposal" | "connectivity",

    // if proposal
    "comm_latency": {min:0, max:0, avg:0},
    "read_latency": {min:0, max:0, avg:0}
    "batch_size": 0

    // if connectivity
    "peer_count": 0,
    "weakly_connected": true | false
}
```