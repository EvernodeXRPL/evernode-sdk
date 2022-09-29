# Events
## Registry client events
| Name                 | Description                                                           |
| -------------------- | --------------------------------------------------------------------- |
| HostRegistered       | Triggered when host registration event is received to the registry.   |
| HostDeregistered     | Triggered when host deregistration event is received to the registry. |
| HostRegUpdated       | Triggered when host sends an update info request.                     |
| Heartbeat            | Triggered when registry receives a heartbeat from a host.             |
| HostPostDeregistered | Triggered after the host is deregistered.                             |
| DeadHostPrune        | Triggered when dead host prone request is received to the registry.   |

## Tenant client events
| Name           | Description                                                     |
| -------------- | --------------------------------------------------------------- |
| AcquireSuccess | Triggered when the tenant receives an acquire success response. |
| AcquireError   | Triggered when the tenant receives an acquire error response.   |
| ExtendSuccess  | Triggered when the tenant receives an extend success response.  |
| ExtendError    | Triggered when the tenant receives an extend error response.    |