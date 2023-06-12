# Events

Following events can be subscribed from Evernode client instances.

## Governor client events
| Name                          | Description                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------- |
| Initiated                     | Triggered when governor receives a hook initialization.                                              |
| CandidateProposed             | Triggered when governor receives a new candidate proposal.                                           |
| CandidateWithdrawn            | Triggered when candidate withdrawal is requested.                                                    |
| ChildHookUpdated              | Triggered when registry or heartbeat hook is updated.                                                |
| GovernanceModeChanged         | Triggered when governor receives a request to change the governance mode.                            |
| DudHostReported               | Triggered when dud host is reported.                                                                 |
| DudHostRemoved                | Triggered when dud host is removed.                                                                  |
| DudHostStatusChanged          | Triggered when dud host candidate election status is changed.                                        |
| FallbackToPiloted             | Triggered when governance mode is changed to piloted mode.                                           |
| NewHookStatusChanged          | Triggered when new hook candidate election status is changed.                                        |
| LinkedDudHostCandidateRemoved | Triggered when a host related to dud host candidate is removed by de-registration/transfer or prune. |

## Registry client events
| Name             | Description                                                            |
| ---------------- | ---------------------------------------------------------------------- |
| HostRegistered   | Triggered when host registration event is received to the registry.    |
| HostDeregistered | Triggered when host de-registration event is received to the registry. |
| HostRegUpdated   | Triggered when host sends an update info request.                      |
| DeadHostPrune    | Triggered when dead host prune request is received to the registry.    |
| HostTransfer     | Triggered when host transfer is requested by the host.                 |
| HostRebate       | Triggered when host rebate is requested by the host.                   |

## Heartbeat client events
| Name            | Description                                                 |
| --------------- | ----------------------------------------------------------- |
| Heartbeat       | Triggered when a heartbeat from a host is received.         |
| FoundationVoted | Triggered when foundation vote for a candidate is received. |

## Host client events
| Name         | Description                                                |
| ------------ | ---------------------------------------------------------- |
| AcquireLease | Triggered when the host receives an lease acquire request. |
| ExtendLease  | Triggered when the host receives an lease extend request.  |

## Tenant client events
| Name           | Description                                                     |
| -------------- | --------------------------------------------------------------- |
| AcquireSuccess | Triggered when the tenant receives an acquire success response. |
| AcquireError   | Triggered when the tenant receives an acquire error response.   |
| ExtendSuccess  | Triggered when the tenant receives an extend success response.  |
| ExtendError    | Triggered when the tenant receives an extend error response.    |
