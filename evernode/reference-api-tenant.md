# Tenant Client

## TenantClient class constructor - `TenantClient(xrpAddress, xrpSecret, options = {})`

### Parameters

Accepts three parameters.
| Name | Type | Description |
| ----------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| xrpAddress | string | XRPL wallet address of the tenant |
| xrpSecret | string | Secret key of the above tenant's XRPL wallet |
| options | object | `(optional)` This can include two properties as below.<pre type="json">{<br> registryAddress: 'r3cNR2bdao1NyvQ5ZuQvCUgqkoWGmgF34E',<br> rippledServer: 'wss://hooks-testnet-v2.xrpl-labs.com'<br>}</pre> You can provide your own `registryAddress` and a `rippledServer` URL. |

### Example

```javascript
const tenantAddress = "rKfHBc8e1VemZPLZoPXB7HjSKU2QjkRfP";
const tenantSecret = "sszyYJ79AdUUF6dR7QfD9ARWfVuz3";

const client = new TenantClient(tenantAddress, tenantSecret, {
  registryAddress: "r3cNR2bdao1NyvQ5ZuQvCUgqkoWGmgF34E",
  rippledServer: "wss://hooks-testnet-v2.xrpl-labs.com",
});
```

<br>

## Acquire Lease - `async acquireLease(hostAddress, requirement, options = {})`

This function is called by a tenant client to acquire an available instance in a certain host. This function can takes three parameters as follows.

### Parameters

| **Name**    | **Type** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ----------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| hostAddress | string   | This is the wallet address of the preferred host that the HotPocket instance will be created in.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| requirement | Object   | The details needed for a successful creation of an instance will be contained in this 'Requirement' object.<br>Ex:<br> <pre type="javascript">{<br> owner*pubkey: "ed5cb83404120ac759609819591ef839b7d222c84f1f08b3012f490586159d2b50",<br> contract_id: "dc411912-bcdd-4f73-af43-32ec45844b9a", <br> image: "hp.latest-ubt.20.04-njs.16",<br> config: {}<br>}</pre> For more details about '\_config*' object , please refer to [**this**](../hotpocket/reference-configuration.md). <br><br> Note: Providing all the configurations herewith can cause _'TRANSACTION_FAILURE'_ error due to exceeding the maximum allowed memo size for now.                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| options     | Object   | `(optional)` This is an optional field and contains necessary configurations for the transaction to be completed.<br>Ex:<br><pre type="javascript">{<br> timeout: 30000,<br> leaseOfferIndex: '',<br> transactionOptions: {}<br>}</pre><br> <table><tr><th>Parameter name</th><th>Type</th><th>Description</th></tr><tr><td>timeout</td><td>number</td><td> This specifies a timeout for the transaction to be completed. It accepts a number and time in milliseconds. This is optional and defaults to 60000 unless provided.</td></tr><tr><td>leaseOfferIndex</td><td>string</td><td> The preferred index of the lease available in the given host. An avaialble offer index will be taken unless this field is provided.</td></tr><tr><td>trasactionOptions</td><td>Object</td><td>During the acquiring process, an NFT-buy-offer transaction takes place. Therefore the [fields defined in the official XRPL documentation for the NFTokenAcceptOffer transaction](https://xrpl.org/nftokenacceptoffer.html#nftokenacceptoffer-fields) can be specified within this object.</td></tr></table> |

### Response Format

Returns a promise that resolves with an object similar to the following sample response object.

```javascript
{
  transaction: {
    Account: 'rEsRKQzg7ZRbk5ikYprR3MzE4ycTLgBLoe',
    Amount: '1',
    Destination: 'rwCtRNp2Hq3q2ozT18dnggxXRxM74ntzXp',
    Fee: '20',
    Flags: 0,
    HookParameters: [ [Object], [Object] ],
    LastLedgerSequence: 3897225,
    Memos: [ [Object] ],
    NetworkID: 21338,
    Sequence: 3897181,
    SigningPubKey: '02E8A636240196A01A7FF60F48FCDF1ACAFD8EFE26B0335BBF76E4251C955361CB',
    TransactionType: 'Payment',
    TxnSignature: '3045022100CA4E8877E966B169E37FCC4226815EF377CA889B68922021108696A19FF0EBA702202167A9C4DC385C4A0AC0E2FA23363A0087B8D006BEE65FBE58627421ED56C4DD',
    date: 739944163,
    hash: '4BC9F85948D7696D834C3682C476BC253E24CD1A081D6B60DACCDF77971CE2A8',
    inLedger: 3897217,
    ledger_index: 3897217
  },
  instance: {
    name: '00010000A547B3F85414A33C2AB38F7788E42AC0F4D2734752BF3969000001CE',
    ip: '65.108.27.90',
    pubkey: 'ed588e604f1385d4b0e917f8b0ace3a16d6dc784e184aaeb30494c1ab7304d298a',
    contract_id: 'dc411912-bcdd-4f73-af43-32ec45844b9a',
    peer_port: '22909',
    user_port: '8129'
  },
  acquireRefId: '5267C171CF0438C21773B26A108021D31F6CFF8AB02A0F99E410669B5B448353'
}
```

The fields in detail:

| Name               | Type             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Account            | string           | The unique address of the account that initiated the transaction.                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Amount             | string           | The amount of currency to deliver.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Destination        | string           | The address of the tenant account which made the acquireLease request.                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Fee                | string           | Integer amount of EVRs, in drops, to be destroyed as a cost for distributing this transaction to the network. Some transaction types have different minimum requirements.                                                                                                                                                                                                                                                                                                                                                                  |
| Flags              | number           | Set of bit-flags for this transaction.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| HookParameters              | array of objects           | This contains 2 objects as follows,<br><br>![image.png](./Assets/Images/acquire-lease-response-hook-parameters.png)<br><br> The field values are in hex format. First object represents the event name which is 'evnAcquireSuccess'. Second object represents the event data.|
| LastLedgerSequence | number           | Highest ledger index this transaction can appear in. Specifying this field places a strict upper limit on how long the transaction can wait to be validated or rejected.                                                                                                                                                                                                                                                                                                                                                                   |
| Memos              | array of Objects | This array contains two objects. The following is an example and the objects are in the same order:<br><br> ![image.png](./Assets/Images/acquire-success-memo-res) <br><br> Data in '_data_' field in each object are encoded in the format mentioned in '_format_' field.<br> 'data' field in first object contains the encrypted information about the instance, which can be found in other fields of the response object.<br>'data' in the second object contains the hex representation of the hash of acquiring request transaction. |
| NetworkID  |  number   |   The networkID is used in the XRPL Hooks library to specify the target network where the hooks will be deployed and executed.    |
| Sequence           | number           | The sequence number of the account sending the transaction. A transaction is only valid if the Sequence number is exactly 1 greater than the previous transaction from the same account.                                                                                                                                                                                                                                                                                                                                                   |
| SigningPubKey      | string           | Hex representation of the public key that corresponds to the private key used to sign this transaction. If an empty string, indicates a multi-signature is present in the Signers field instead.                                                                                                                                                                                                                                                                                                                                           |
| TransactionType    | string           | The type of transaction. In this situation, it is always _'Payment'_                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| TxnSignature       | string           | (Automatically added when signing) The signature that verifies this transaction as originating from the account it says it is from.                                                                                                                                                                                                                                                                                                                                                                                                        |
| date               | number           | A number of seconds since January 1, 2000 (00:00 UTC) indicating the close time of the ledger in which the transaction was applied. This value does not have a precise relationship with physical time, and is dependent on the close time resolution.                                                                                                                                                                                                                                                                                     |
| hash               | string           | The SHA-512 hash of the transaction.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| inLedger           | number           | (Deprecated) Alias for ledger_index.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ledger_index       | number           | The ledger index of the ledger that includes this transaction.                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| name               | string           | The unique identifier of the instance. This is the NFToken Id acquired during the registration.                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ip                 | string           | The public IP address of the host server.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| contract_id        | string           | The unique contract identifier.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| peer_port          | string           | The port used for the communication among the instances in the cluster.                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| user_port          | string           | The port used for the communication between the user and the instance.                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| acquireRefId       | string           | The hash of the transaction that requested the initial acquiring of the instance.                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

### Error Response

On error, this functions rejects the promise with an error object similar to the following,

```javascript
{
  error: 'ACQUIRE_ERR',
  transaction: {
    Account: 'rGhvPCX1x2qyUTEEGQNneVYwgwXWYCvp8P',
    Amount: {
      currency: 'EVR',
      issuer: 'rfxLPXCcSmwR99dV97yFozBzrzpvCa2VCf',
      value: '2'
    },
    Fee: '20',
    Flags: 0,
    HookParameters: [ [Object], [Object] ],
    LastLedgerSequence: 6353795,
    Memos: [ [Object], [Object] ],
    NetworkID: 21338,
    Sequence: 3883005,
    SigningPubKey: '03A8141293AEEE6C44CA7D80095042234924009FA2B3FA8E693D99A5069BACA37C',
    TransactionType: 'URITokenBuy',
    TxnSignature: '30450221009EB0EA5C8E9C712A49D243C6DED0C8718373DDABB6643DF118BAECA151C892D902202B402E2CCF93207E1B2A499BD1659102CE53DF3D7EBB97D351AFA30A6A615667',
    date: 739879611,
    hash: '5E60E35EE75CF62168FDFCF5D26C607F0AF3BEF03075E57ADE3E01428DC361D5',
    inLedger: 6353787,
    ledger_index: 6353787
  },
  reason: 'docker_image_invalid',
  acquireRefId: 'E7D75DEFDD1863638FC9B372ACDB62F206DFAF5F46007B86C2A1446201FD8BB6'
}
```

The fields other than those mentioned in the successful response section above, are as follows,

| Field  | Type             | Description                                                                                                                                                                           |
| ------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| error  | string           | Error code: 'ACQUIRE_ERR'                                                                                                                                                             |
| reason | string           | Reason for the failure of acquisition.                                                                                                                                                |
| Amount | object           | An instance acquisition costs 2 EVRs and this transaction returns that 2 EVRs back due to acquisition failure. This object contains the three fields shown in the example code above. |
| Memos  | array of objects | This array contains two objects. The following is an example and the objects are in the same order in the response:<br> ![image](./Assets/Images/memo-error-res.png)                  |

### Example

```javascript
const evernode = require("evernode-js-client");

async function acquire() {
  const tenant = new evernode.TenantClient(tenantAddress, tenantSecret);
  await tenant.connect();

  try {
    const timeout = 10000;
    const hostAddress = "rnET2YR19WDP4vB8XtDhcF2J4afqMM6xim";

    const result = await tenant.acquireLease(
      hostAddress,
      {
        owner_pubkey:
          "ed5cb83404120ac759609819591ef839b7d222c84f1f08b3012f490586159d2b50",
        contract_id: "dc411912-bcdd-4f73-af43-32ec45844b9a",
        image: "hp.latest-ubt.20.04-njs.16",
        config: {},
      },
      { timeout: timeout }
    );
    console.log("Tenant received instance ", result);
  } catch (err) {
    console.log("Tenant received acquire error: ", err.reason);
  }
}
```

<br>

## Acquire Lease Submit - `acquireLeaseSubmit(hostAddress, requirement, options = {})`

This function is called by a tenant client to submit the acquire lease transaction in a certain host. This function is called within the [acquireLease function](#acquire-lease---async-acquireleasehostaddress-requirement-options---).

This accepts the same parameters passed to the acquireLease function.

### Parameters

| **Parameter** | **Type** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| hostAddress   | string   | This is the wallet address of the preferred host that the HotPocket instance will be created in.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| requirement   | object   | The details needed for a successful creation of an instance will be contained in this 'Requirement' object.<br>Ex:<br> <pre lang="javascript">{<br> owner*pubkey: "ed5cb83404120ac759609819591ef839b7d222c84f1f08b3012f490586159d2b50",<br> contract_id: "dc411912-bcdd-4f73-af43-32ec45844b9a", <br> image: "hp.latest-ubt.20.04-njs.16",<br> config: {}<br>}</pre> For more details about '\_config*' object , please refer to [**this**](./../hotpocket/reference-configuration.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| options       | object   | `(optional)` This is an optional field and contains necessary configurations for the transaction to be completed.<br>Ex:<br><pre lang="javascript">{<br> timeout: 30000,<br> leaseOfferIndex: '',<br> transactionOptions: {}<br>}</pre><br> <table><tr><th>Parameter name</th><th>Type</th><th>Description</th></tr><tr><td>timeout</td><td>number</td><td> This specifies a timeout for the transaction to be completed. It accepts a number and time in milliseconds. This is optional and defaults to 60000 unless provided.</td></tr><tr><td>leaseOfferIndex</td><td>string</td><td> The preferred index of the lease available in the given host. An avaialble offer index will be taken unless this field is provided.</td></tr><tr><td>trasactionOptions</td><td>Object</td><td>During the acquiring process, an NFT-buy-offer transaction takes place. Therefore the [fields defined in the official XRPL documentation for the NFTokenAcceptOffer transaction](https://xrpl.org/nftokenacceptoffer.html#nftokenacceptoffer-fields) can be specified within this object.</td></tr></table> |

### Response Format

Returns a promise that resolves with an object similar to the following sample response object.

```javascript
{
  id: '5267C171CF0438C21773B26A108021D31F6CFF8AB02A0F99E410669B5B448353',
  code: 'tesSUCCESS',
  details: {
    Account: 'rwCtRNp2Hq3q2ozT18dnggxXRxM74ntzXp',
    Amount: {
      currency: 'EVR',
      issuer: 'ra328vuQhL5fKrjqGB3FzVM45a5zuNS2KR',
      value: '2'
    },
    Fee: '20',
    Flags: 0,
    HookParameters: [ [Object] ],
    LastLedgerSequence: 3897222,
    Memos: [ [Object] ],
    NetworkID: 21338,
    Sequence: 3897180,
    SigningPubKey: '035D6AF15C705505D92C198900E075E80A71B8F7BB3C87E1D5173DFECDBEFEE69F',
    TransactionType: 'URITokenBuy',
    TxnSignature: '304402205E5DC9349740CD8115B81879AD20EE45706B2AF505DE07E9F89EC902AEBCA57F022023BEB2172A70948011A1E105AAF0FD081F94CD14EF74D36D8AB46EB15207F7C2',
    URITokenID: '34FB3A3426DB37FF69D9BDF62AB503B858BC7BEF24BD5F97CCBED17A61D4A6C5',
    ctid: 'C03B777E0000535A',
    date: 739944160,
    hash: '5267C171CF0438C21773B26A108021D31F6CFF8AB02A0F99E410669B5B448353',
    inLedger: 3897214,
    ledger_index: 3897214,
    meta: {
      AffectedNodes: [Array],
      TransactionIndex: 0,
      TransactionResult: 'tesSUCCESS'
    },
    validated: true
  }
}
```

The fields in detail:

| Name               | Type             | Description                                                                                                                                                                                                                                                                         |
| ------------------ | ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id                 | string           | The unique hash of the transaction.|
| code               | string           | Status code of the transaction. It is '_tesSUCCESS_' if the transaction is successful. |
| Account            | string           | The unique address of the XRPL account that initiated the transaction.|
| Amount                | number           | The amount of currency to deliver.|
| Fee                | string           | Integer amount of XRP, in drops, to be destroyed as a cost for distributing this transaction to the network.|
| Flags              | number           | Set of bit-flags for this transaction.|
| HookParameters              | array of objects           | This contains an object as follows,<br><br>![image.png](./Assets/Images/acquire-lease-submit-hook-parameters.png)<br><br> The field values are in hex format. This object represents the event name. If decoded, event name is 'evnAcquireLease'.|
| LastLedgerSequence | number           | Highest ledger index this transaction can appear in. Specifying this field places a strict upper limit on how long the transaction can wait to be validated or rejected.                                                                                                            |
| Memos              | array of objects | This contains an object as follows,<br><br>![image.png](./Assets/Images/acquire-submit-lease-success-memo.png)<br><br> The field values are in hex format. If decoded, 'MemoType' has 'evnAcquireLease' and 'MemoFormat' has 'base64' as values. 'MemoData' has a string in base64. |
| NetworkID   | number           | The networkID is used in the XRPL Hooks library to specify the target network where the hooks will be deployed and executed.|
| Sequence           | number           | The sequence number of the account sending the transaction. A transaction is only valid if the Sequence number is exactly 1 greater than the previous transaction from the same account.                                                                                            |
| SigningPubKey      | string           | Hex representation of the public key that corresponds to the private key used to sign this transaction. If an empty string, indicates a multi-signature is present in the Signers field instead.                                                                                    |
| TransactionType    | string           | The type of transaction. In this situation, it is always _'URITokenBuy'_                                                                                                                                                                                                     |
| TxnSignature       | string           | (Automatically added when signing) The signature that verifies this transaction as originating from the account it says it is from.                                                                                                                                                 |
| URITokenID                 | string           | kasun blank.|
| ctid                 | string           | kasun blank.|
| date               | number           | A number of seconds since January 1, 2000 (00:00 UTC) indicating the close time of the ledger in which the transaction was applied. This value does not have a precise relationship with physical time, and is dependent on the close time resolution.                              |
| hash               | string           | The SHA-512 hash of the transaction.                                                                                                                                                                                                                                                |
| inLedger           | number           | (Deprecated) Alias for ledger_index.                                                                                                                                                                                                                                                |
| ledger_index       | number           | The ledger index of the ledger that includes this transaction.                                                                                                                                                                                                                      |
| validated          | boolean          | Whether the transaction is validated or not.                                                                                                                                                                                                                                        |

### Error Responses

This method makes an NFTokenAcceptOffer transaction. Therefore, this can raise [these errors](https://xrpl.org/nftokenacceptoffer.html#error-cases) explained in the XRPL official documentation.

### Example

```javascript
const tx = await this.acquireLeaseSubmit(hostAddress, requirement);
```

<br>

## Watch Acquire Response - `async watchAcquireResponse(tx, options = {})`

This function watches for an acquire-success response(transaction) and returns the response or throws the error response on acquire-error response from the host XRPL account. This function is called within the [_acquireLease_](#acquire-lease---async-acquireleasehostaddress-requirement-options---) function.
This accepts two parameters as below.

### Parameters

| Name    | Type   | Description                                                                                                                                        |
| ------- | ------ | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| tx      | object | The response object resolved by the [_acquireLeaseSubmit_](#acquire-lease-submit---acquireleasesubmithostaddress-requirement-options---) function. |
| options | object | `(optional)`The same option object passed to the [_acquireLease_](#acquire-lease---async-acquireleasehostaddress-requirement-options---) function. |

### Response Format

Returns a promise that resolves with an object similar to the following sample response object.

```javascript
{
  transaction: {
    Account: 'rEsRKQzg7ZRbk5ikYprR3MzE4ycTLgBLoe',
    Amount: '1',
    Destination: 'rwCtRNp2Hq3q2ozT18dnggxXRxM74ntzXp',
    Fee: '20',
    Flags: 0,
    HookParameters: [ [Object], [Object] ],
    LastLedgerSequence: 3897225,
    Memos: [ [Object] ],
    NetworkID: 21338,
    Sequence: 3897181,
    SigningPubKey: '02E8A636240196A01A7FF60F48FCDF1ACAFD8EFE26B0335BBF76E4251C955361CB',
    TransactionType: 'Payment',
    TxnSignature: '3045022100CA4E8877E966B169E37FCC4226815EF377CA889B68922021108696A19FF0EBA702202167A9C4DC385C4A0AC0E2FA23363A0087B8D006BEE65FBE58627421ED56C4DD',
    date: 739944163,
    hash: '4BC9F85948D7696D834C3682C476BC253E24CD1A081D6B60DACCDF77971CE2A8',
    inLedger: 3897217,
    ledger_index: 3897217
  },
  instance: {
    name: '00010000A547B3F85414A33C2AB38F7788E42AC0F4D2734752BF3969000001CE',
    ip: '65.108.27.90',
    pubkey: 'ed588e604f1385d4b0e917f8b0ace3a16d6dc784e184aaeb30494c1ab7304d298a',
    contract_id: 'dc411912-bcdd-4f73-af43-32ec45844b9a',
    peer_port: '22909',
    user_port: '8129'
  },
  acquireRefId: '5267C171CF0438C21773B26A108021D31F6CFF8AB02A0F99E410669B5B448353'
}
```

The fields in detail:

| Name               | Type             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Account            | string           | The unique address of the account that initiated the transaction.                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Amount             | string           | The amount of currency to deliver.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Destination        | string           | The address of the tenant account which made the acquireLease request.                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Fee                | string           | Integer amount of EVRs, in drops, to be destroyed as a cost for distributing this transaction to the network. Some transaction types have different minimum requirements.                                                                                                                                                                                                                                                                                                                                                                  |
| Flags              | number           | Set of bit-flags for this transaction.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| HookParameters              | array of objects           | This contains 2 objects as follows,<br><br>![image.png](./Assets/Images/watch-acquire-response-hook-parameters.png)<br><br> The field values are in hex format. First object represents the event name which is 'evnAcquireSuccess'. Second object represents the event data.|
| LastLedgerSequence | number           | Highest ledger index this transaction can appear in. Specifying this field places a strict upper limit on how long the transaction can wait to be validated or rejected.                                                                                                                                                                                                                                                                                                                                                                   |
| Memos              | array of Objects | This array contains two objects. The following is an example and the objects are in the same order:<br><br> ![image.png](./Assets/Images/acquire-success-memo-res) <br><br> Data in '_data_' field in each object are encoded in the format mentioned in '_format_' field.<br> 'data' field in first object contains the encrypted information about the instance, which can be found in other fields of the response object.<br>'data' in the second object contains the hex representation of the hash of acquiring request transaction. |
| NetworkID  |  number   |   The networkID is used in the XRPL Hooks library to specify the target network where the hooks will be deployed and executed.    |
| Sequence           | number           | The sequence number of the account sending the transaction. A transaction is only valid if the Sequence number is exactly 1 greater than the previous transaction from the same account.                                                                                                                                                                                                                                                                                                                                                   |
| SigningPubKey      | string           | Hex representation of the public key that corresponds to the private key used to sign this transaction. If an empty string, indicates a multi-signature is present in the Signers field instead.                                                                                                                                                                                                                                                                                                                                           |
| TransactionType    | string           | The type of transaction. In this situation, it is always _'Payment'_                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| TxnSignature       | string           | (Automatically added when signing) The signature that verifies this transaction as originating from the account it says it is from.                                                                                                                                                                                                                                                                                                                                                                                                        |
| date               | number           | A number of seconds since January 1, 2000 (00:00 UTC) indicating the close time of the ledger in which the transaction was applied. This value does not have a precise relationship with physical time, and is dependent on the close time resolution.                                                                                                                                                                                                                                                                                     |
| hash               | string           | The SHA-512 hash of the transaction.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| inLedger           | number           | (Deprecated) Alias for ledger_index.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ledger_index       | number           | The ledger index of the ledger that includes this transaction.                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| name               | string           | The unique identifier of the instance. This is the NFToken Id acquired during the registration.                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ip                 | string           | The public IP address of the host server.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| contract_id        | string           | The unique contract identifier.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| peer_port          | string           | The port used for the communication among the instances in the cluster.                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| user_port          | string           | The port used for the communication between the user and the instance.                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| acquireRefId       | string           | The hash of the transaction that requested the initial acquiring of the instance.                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

### Example

```javascript
const response = await watchAcquireResponse(tx);
```

<br>

## Extend Lease - `async extendLease(hostAddress, moments, instanceName, options = {})`

This function is called by a tenant client to extend an available instance in certain host. This function can take four parameters as follows.

### Parameters

| Name         | Type   | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| hostAddress  | string | HostAddress is a cryptographic code that allows a user to receive cryptocurrencies. <br><br> example: "rKtuh3pGwkPk86BuVrPNS58JkyMuz79DbB"                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| moments      | number | Moment is 1190 ledgers (est. 1 hour).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| instanceName | string | Tenant received instance name. this name can be retrieve by performing [acquire Lease](#acquire-lease---async-acquireleasehostaddress-requirement-options).                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| options      | object | This is an optional field and contains necessary details for the transactions. The attributes are explained down below. <br><br> **transactionOptions** - During the extending lease process, a [Payment](https://xrpl.org/payment.html#payment) transaction takes place. Therefore the fields defined in the official XRPL documentation for the Payment transaction can be specified within this object. <br><br> **timeout** - This specifies a timeout for the transaction to be completed. it accepts a number and time in milliseconds. this is optional and defaults to 60000 unless provided. |

### Response Format

An example of a successful response:

```
exampleResponse {
  transaction: {
    Account: 'rEsRKQzg7ZRbk5ikYprR3MzE4ycTLgBLoe',
    Amount: '1',
    Destination: 'rwCtRNp2Hq3q2ozT18dnggxXRxM74ntzXp',
    Fee: '20',
    Flags: 0,
    HookParameters: [ [Object], [Object] ],
    LastLedgerSequence: 3897238,
    Memos: [ [Object] ],
    NetworkID: 21338,
    Sequence: 3897182,
    SigningPubKey: '02E8A636240196A01A7FF60F48FCDF1ACAFD8EFE26B0335BBF76E4251C955361CB',
    TransactionType: 'Payment',
    TxnSignature: '3045022100E9964100E5F0A16B9B51BBC5F6590F985BDA1775CB78A60EBAD1BE19014BBF0102206D193007045631DBD01AF493B854CCAB9F8F9E0F8B0A8F2D4080288FCEC36B07',
    date: 739944210,
    hash: 'AD8B3E05A85A1262A9C41C0EBFABA935A0B5C8350726387B11BC37A4E6D6E28F',
    inLedger: 3897230,
    ledger_index: 3897230
  },
  expiryMoment: 5310,
  extendeRefId: 'CC3A5C7D8CE4DF0962A8E702EBD2BFA439D4FEC1C3F72AF2BF18604FE5F1675E'
}
```

Now let's see the details of the response fields.

| Name               | Type             | Description                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Account            | string           | The unique address of the [account](https://xrpl.org/accounts.html) that initiated the transaction.|
| Amount             | string           | To specify an amount of XRP, use a String Number indicating drops of XRP, where each drop is equal to 0.000001 XRP. <br><br>For example, to specify 13.1 XRP : "13100000"|
| Destination        | string           | Tenant xrpl address that made the extend lease request.|
| Fee                | string           | Integer amount of EVRs, in drops, to be destroyed as a cost for distributing this transaction to the network. Some transaction types have different minimum requirements.|
| Flags              | number           | Set of bit-flags for this transaction.|
| HookParameters              | array of objects           | This contains 2 objects as follows,<br><br>![image.png](./Assets/Images/extend-lease-hook-parameters.png)<br><br> The field values are in hex format. First object represents the event name which is 'evnExtendSuccess'. Second object represents the event data.|
| LastLedgerSequence | number           | Highest ledger index this transaction can appear in. Specifying this field places a strict upper limit on how long the transaction can wait to be validated or rejected.|
| Memos              | array of objects | Additional arbitrary information used to identify this transaction.|
|  NetworkID   |    number   |       The networkID is used in the XRPL Hooks library to specify the target network where the hooks will be deployed and executed.      |
| Sequence           | number           | The sequence number of the account sending the transaction. A transaction is only valid if the Sequence number is exactly 1 greater than the previous transaction from the same account.                                                                                                                                                                                                         |
| SigningPubKey      | string           | Hex representation of the public key that corresponds to the private key used to sign this transaction. If an empty string, indicates a multi-signature is present in the Signers field instead.                                                                                                                                                                                                 |
| TransactionType    | string           | The type of transaction. Valid types include: Payment                                                                                                                                                                                                                                                                                                                                            |
| TxnSignature       | string           | (Automatically added when signing) The signature that verifies this transaction as originating from the account it says it is from.                                                                                                                                                                                                                                                              |
| date               | number           | A number of seconds since January 1, 2000 (00:00 UTC) indicating the close time of the ledger in which the transaction was applied. This value does not have a precise relationship with physical time, and is dependent on the close time resolution.                                                                                                                                           |
| hash               | string           | Every signed transaction has a unique "hash" that identifies it. The server provides the hash in the response when you submit the transaction.|
| inLedger           | number           | (Deprecated) Alias for ledger_index.                                                                                                                                                                                                                   |
| ledger_index       | number           | The ledger index of the ledger that includes this transaction.                                                                                                                                                                                         |
| expiryMoment       | number           | Defines which moment instance going to expire.                                                                                                                                                                                                                                                                                                                                                   |
| extendeRefId       | string           | Transaction ID of the extend request.                                                                                                                                                                                                                                                                                                                                                            |

### Example

```javascript
const result = await tenant.extendLease(hostAddress, moments, instanceName, {
  timeout: timeout,
});
```

<br>

## Extend Lease Submit - `async extendLeaseSubmit(hostAddress, amount, tokenID, options = {})`

This function is called by a tenant client to submit the extend lease transaction in certain host. This function will be called inside extendLease function. This function can take four parameters as follows.

| Name        | Type   | Description                                                                                                                                                                                                                                                                                                                                                                                                |
| ----------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| hostAddress | string | HostAddress is a cryptographic code that allows a user to receive cryptocurrencies. <br><br> example: "rKtuh3pGwkPk86BuVrPNS58JkyMuz79DbB"                                                                                                                                                                                                                                                                 |
| amount      | number | Cost for the extended moments , in EVRs.                                                                                                                                                                                                                                                                                                                                                                   |
| tokenID     | string | Tenant received instance name. this name can be retrieve by performing [acquire Lease](#acquire-lease---async-acquireleasehostaddress-requirement-options).                                                                                                                                                                                                                                                |
| options     | object | This is an optional field and contains necessary details for the transactions. The attributes are explained down below. <br><br> **transactionOptions** - During the extending lease process, a [Payment](https://xrpl.org/payment.html#payment) transaction takes place. Therefore the fields defined in the official XRPL documentation for the Payment transaction can be specified within this object. |

## Response Format

An example of a successful response:

```
exampleResponse  {
  id: 'CC3A5C7D8CE4DF0962A8E702EBD2BFA439D4FEC1C3F72AF2BF18604FE5F1675E',
  code: 'tesSUCCESS',
  details: {
    Account: 'rwCtRNp2Hq3q2ozT18dnggxXRxM74ntzXp',
    Amount: {
      currency: 'EVR',
      issuer: 'ra328vuQhL5fKrjqGB3FzVM45a5zuNS2KR',
      value: '4'
    },
    Destination: 'rEsRKQzg7ZRbk5ikYprR3MzE4ycTLgBLoe',
    Fee: '20',
    Flags: 0,
    HookParameters: [ [Object], [Object] ],
    LastLedgerSequence: 3897235,
    Memos: [],
    NetworkID: 21338,
    Sequence: 3897182,
    SigningPubKey: '035D6AF15C705505D92C198900E075E80A71B8F7BB3C87E1D5173DFECDBEFEE69F',
    TransactionType: 'Payment',
    TxnSignature: '3045022100898E2E9583EBBB1A651E85CC27C97D04F6523C8568FD409C9955602EA400B60802204F6352E1C16C49A0B8BCC9D356C5440DFD839D25C51C89342CEF3DA8002500D0',
    ctid: 'C03B778B0002535A',
    date: 739944200,
    hash: 'CC3A5C7D8CE4DF0962A8E702EBD2BFA439D4FEC1C3F72AF2BF18604FE5F1675E',
    inLedger: 3897227,
    ledger_index: 3897227,
    meta: {
      AffectedNodes: [Array],
      TransactionIndex: 2,
      TransactionResult: 'tesSUCCESS',
      delivered_amount: [Object]
    },
    validated: true
  }
}
```

Now let's see the details of the response fields.

| Name               | Type             | Description                                                                                                                                                                                                                                            |
| ------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| id                 | string           | The unique hash of the transaction.                                                                                                                                                                                                                    |
| code               | string           | Status code of the transaction. It is '_tesSUCCESS_' if the transaction is successful.                                                                                                                                                                 |
| Account            | string           | The unique address of the [account](https://xrpl.org/accounts.html) that initiated the transaction.                                                                                                                                                    |
| Amount             | string           | To specify an amount of XRP, use a String Number indicating drops of XRP, where each drop is equal to 0.000001 XRP. <br><br>For example, to specify 13.1 XRP : "13100000"                                                                              |
| Destination        | string           | Tenant xrpl address that made the extend lease request.                                                                                                                                                                                                |
| Fee                | string           | Integer amount of EVRs, in drops, to be destroyed as a cost for distributing this transaction to the network. Some transaction types have different minimum requirements.                                                                              |
| Flags              | number           | Set of bit-flags for this transaction.|
| HookParameters              | array of objects           | This contains 2 objects as follows,<br><br>![image.png](./Assets/Images/extend-lease-submit-hook-parameters.png)<br><br> The field values are in hex format. First object represents the event name which is 'evnExtendLease' after decoding. Second object represents the event data.|
| LastLedgerSequence | number           | Highest ledger index this transaction can appear in. Specifying this field places a strict upper limit on how long the transaction can wait to be validated or rejected.                                                                               |
| Memos              | array of objects | Additional arbitrary information used to identify this transaction.|
|  NetworkID   |   number   |  The networkID is used in the XRPL Hooks library to specify the target network where the hooks will be deployed and executed.   |
| Sequence           | number           | The sequence number of the account sending the transaction. A transaction is only valid if the Sequence number is exactly 1 greater than the previous transaction from the same account.                                                               |
| SigningPubKey      | string           | Hex representation of the public key that corresponds to the private key used to sign this transaction. If an empty string, indicates a multi-signature is present in the Signers field instead.                                                       |
| TransactionType    | string           | The type of transaction. Valid types include: Payment                                                                                                                                                                                                  |
| TxnSignature       | string           | (Automatically added when signing) The signature that verifies this transaction as originating from the account it says it is from.|
|  ctid  | string  |  kasun blank   |
| date               | number           | A number of seconds since January 1, 2000 (00:00 UTC) indicating the close time of the ledger in which the transaction was applied. This value does not have a precise relationship with physical time, and is dependent on the close time resolution. |
| hash               | string           | Every signed transaction has a unique "hash" that identifies it. The server provides the hash in the response when you submit the transaction.                                                                                                         |
| inLedger           | number           | (Deprecated) Alias for ledger_index.                                                                                                                                                                                                                   |
| ledger_index       | number           | The ledger index of the ledger that includes this transaction.                                                                                                                                                                                         |
| validated          | boolean          | Whether the transaction is validated or not.                                                                                                                                                                                                           |

## Example

```javascript
const tx = await this.extendLeaseSubmit(hostAddress, amount, tokenID, options);
```

<br>

## Watch Extend Response - `async watchExtendResponse(tx, options = {})`

This function watches for an extendlease-success response(transaction) and returns the response or throws the error response on extendlease-error response from the host XRPL account. This function is called within the [_extendLease_](#extend-lease---async-acquireleasehostaddress-requirement-options---) function.

### Parameters

| Name    | Type   | Description                                                                                                                                                                                                                                                                                                       |
| ------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| tx      | object | Response of [extendLeaseSubmit](#extend-lease-submit)                                                                                                                                                                                                                                                             |
| options | object | This is an optional field and contains necessary details for the transactions. The attributes are explained down below. <br><br>**timeout** - This specifies a timeout for the transaction to be completed. it accepts a number and time in milliseconds. this is optional and defaults to 60000 unless provided. |

### Response Format

An example of a successful response:

```
exampleResponse {
  transaction: {
    Account: 'rEsRKQzg7ZRbk5ikYprR3MzE4ycTLgBLoe',
    Amount: '1',
    Destination: 'rwCtRNp2Hq3q2ozT18dnggxXRxM74ntzXp',
    Fee: '20',
    Flags: 0,
    HookParameters: [ [Object], [Object] ],
    LastLedgerSequence: 3897238,
    Memos: [ [Object] ],
    NetworkID: 21338,
    Sequence: 3897182,
    SigningPubKey: '02E8A636240196A01A7FF60F48FCDF1ACAFD8EFE26B0335BBF76E4251C955361CB',
    TransactionType: 'Payment',
    TxnSignature: '3045022100E9964100E5F0A16B9B51BBC5F6590F985BDA1775CB78A60EBAD1BE19014BBF0102206D193007045631DBD01AF493B854CCAB9F8F9E0F8B0A8F2D4080288FCEC36B07',
    date: 739944210,
    hash: 'AD8B3E05A85A1262A9C41C0EBFABA935A0B5C8350726387B11BC37A4E6D6E28F',
    inLedger: 3897230,
    ledger_index: 3897230
  },
  expiryMoment: 5341,
  extendRefId: 'CC3A5C7D8CE4DF0962A8E702EBD2BFA439D4FEC1C3F72AF2BF18604FE5F1675E'
}
```

Now let's see the details of the response fields.

| Name               | Type             | Description                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Account            | string           | The unique address of the [account](https://xrpl.org/accounts.html) that initiated the transaction.                                                                                                                                                                                                                                                                                              |
| Amount             | string           | To specify an amount of XRP, use a String Number indicating drops of XRP, where each drop is equal to 0.000001 XRP. <br><br>For example, to specify 13.1 XRP : "13100000"                                                                                                                                                                                                                        |
| Destination        | string           | Tenant xrpl address that made the extend lease request.                                                                                                                                                                                                                                                                                                                                          |
| Fee                | string           | Integer amount of EVRs, in drops, to be destroyed as a cost for distributing this transaction to the network. Some transaction types have different minimum requirements.                                                                                                                                                                                                                        |
| Flags              | number           | Set of bit-flags for this transaction.|
| HookParameters              | array of objects           | This contains 2 objects as follows,<br><br>![image.png](./Assets/Images/watch-extend-response-hook-parameters.png)<br><br> The field values are in hex format. First object represents the event name which is 'evnExtendSuccess'. Second object represents the event data.|
| LastLedgerSequence | number           | Highest ledger index this transaction can appear in. Specifying this field places a strict upper limit on how long the transaction can wait to be validated or rejected.                                                                                                                                                                                                                         |
| Memos              | array of objects | Additional arbitrary information used to identify this transaction.|
|  NetworkID   |    number     |    The networkID is used in the XRPL Hooks library to specify the target network where the hooks will be deployed and executed. |
| Sequence           | number           | The sequence number of the account sending the transaction. A transaction is only valid if the Sequence number is exactly 1 greater than the previous transaction from the same account.                                                                                                                                                                                                         |
| SigningPubKey      | string           | Hex representation of the public key that corresponds to the private key used to sign this transaction. If an empty string, indicates a multi-signature is present in the Signers field instead.                                                                                                                                                                                                 |
| TransactionType    | string           | The type of transaction. Valid types include: Payment                                                                                                                                                                                                                                                                                                                                            |
| TxnSignature       | string           | (Automatically added when signing) The signature that verifies this transaction as originating from the account it says it is from.                                                                                                                                                                                                                                                              |
| date               | number           | A number of seconds since January 1, 2000 (00:00 UTC) indicating the close time of the ledger in which the transaction was applied. This value does not have a precise relationship with physical time, and is dependent on the close time resolution.                                                                                                                                           |
| hash               | string           | Every signed transaction has a unique "hash" that identifies it. The server provides the hash in the response when you submit the transaction.|
| inLedger           | number           | (Deprecated) Alias for ledger_index.                                                                                                                                                                                                                   |
| ledger_index       | number           | The ledger index of the ledger that includes this transaction.                                                                                                                                                                                         |
| expiryMoment       | number           | Defines which moment instance going to expire.                                                                                                                                                                                                                                                                                                                                                   |

## Example

```javascript
const response = await this.watchExtendResponse(tx, options);
```
