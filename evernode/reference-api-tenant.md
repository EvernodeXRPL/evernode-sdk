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
    Account: 'rGhvPCX1x2qyUTEEGQNneVYwgwXWYCvp8P',
    Amount: '1',
    Destination: 'rKfHBc8e1VemZPLZoPXB7HjSKU2QjkRfP',
    Fee: '20',
    Flags: 0,
    LastLedgerSequence: 6326068,
    Memos: [ [Object], [Object] ],
    Sequence: 3882901,
    SigningPubKey: '03A8141293AEEE6C44CA7D80095042234924009FA2B3FA8E693D99A5069BACA37C',
    TransactionType: 'Payment',
    TxnSignature: '304402206D6814A850A210596349148AE820B9F68280765496C9F8D48D9BBA009CA0C5220220529FF3FB10CD4D1475133FB79AF45B1008CC258A275CA89F624F3CF17341713E',
    date: 717680731,
    hash: '355E175AADF093BFD1EA5EA8EB6FC0949706D3AEF0DAA936F61B9888C74B9DEA',
    inLedger: 6326059,
    ledger_index: 6326059
  },
  instance: {
    name: '00010000A547B3F85414A33C2AB38F7788E42AC0F4D2734752BF3969000001CE',
    ip: '65.108.27.90',
    pubkey: 'ed588e604f1385d4b0e917f8b0ace3a16d6dc784e184aaeb30494c1ab7304d298a',
    contract_id: 'dc411912-bcdd-4f73-af43-32ec45844b9a',
    peer_port: '22909',
    user_port: '8129'
  },
  acquireRefId: 'C02F15FD84B1ADE6C608F2C76D2D9BACEE9BBB513E1B46CCA738142BE1BD2CCB'
}
```

The fields in detail:

| Name               | Type             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Account            | string           | The unique address of the account that initiated the transaction.                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Amount             | string           | The amount of currency to deliver.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Destination        | string           | The address of the tenant account which made the acquireLease request.                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Fee                | string           | Integer amount of Evers, in drops, to be destroyed as a cost for distributing this transaction to the network. Some transaction types have different minimum requirements.                                                                                                                                                                                                                                                                                                                                                                 |
| Flags              | number           | Set of bit-flags for this transaction.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| LastLedgerSequence | number           | Highest ledger index this transaction can appear in. Specifying this field places a strict upper limit on how long the transaction can wait to be validated or rejected.                                                                                                                                                                                                                                                                                                                                                                   |
| Memos              | array of Objects | This array contains two objects. The following is an example and the objects are in the same order:<br><br> ![image.png](./Assets/Images/acquire-success-memo-res) <br><br> Data in '_data_' field in each object are encoded in the format mentioned in '_format_' field.<br> 'data' field in first object contains the encrypted information about the instance, which can be found in other fields of the response object.<br>'data' in the second object contains the hex representation of the hash of acquiring request transaction. |
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
    Destination: 'rKfHBc8e1VemZPLZoPXB7HjSKU2QjkRfP',
    Fee: '20',
    Flags: 0,
    LastLedgerSequence: 6353795,
    Memos: [ [Object], [Object] ],
    Sequence: 3883005,
    SigningPubKey: '03A8141293AEEE6C44CA7D80095042234924009FA2B3FA8E693D99A5069BACA37C',
    TransactionType: 'Payment',
    TxnSignature: '30450221009EB0EA5C8E9C712A49D243C6DED0C8718373DDABB6643DF118BAECA151C892D902202B402E2CCF93207E1B2A499BD1659102CE53DF3D7EBB97D351AFA30A6A615667',
    date: 717764952,
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
const evernode = require("evernode");

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
  id: 'F0DF09BFF4315CA5F44DB0C7E1998E7EEDDFFC52AAFFAD5D09E1B572A0F8557F',
  code: 'tesSUCCESS',
  details: {
    Account: 'rKfHBc8e1VemZPLZoPXB7HjSKU2QjkRfP',
    Fee: '20',
    Flags: 0,
    LastLedgerSequence: 6347339,
    Memos: [ [Object] ],
    NFTokenSellOffer: '2492241ABCCFC87E78B868578A7DC79601F1C1F81802E2495662CF374FDDC540',
    Sequence: 6268901,
    SigningPubKey: '02439CED77D15223689BFABC3B0F687B6B3AA412184E154F7088D9519F1ECD9BDD',
    TransactionType: 'NFTokenAcceptOffer',
    TxnSignature: '304402207411337B5EFF4C86CFF90B8D41F0D813C3C269AD342AC039A494022B268798770220531CF3DEF9F642DE7245C2EB6649EC24876245B4DD06F781FA506ED2C2DB0ECD',
    date: 717745331,
    hash: 'F0DF09BFF4315CA5F44DB0C7E1998E7EEDDFFC52AAFFAD5D09E1B572A0F8557F',
    inLedger: 6347331,
    ledger_index: 6347331,
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
| Account            | string           | The unique address of the XRPL account that initiated the transaction.                                                                                                                                                                                                              |
| id                 | string           | The unique hash of the transaction                                                                                                                                                                                                                                                  |
| code               | string           | Status code of the transaction. It is '_tesSUCCESS_' if the transaction is successful.                                                                                                                                                                                              |
| Fee                | string           | Integer amount of XRP, in drops, to be destroyed as a cost for distributing this transaction to the network.                                                                                                                                                                        |
| Flags              | number           | Set of bit-flags for this transaction.                                                                                                                                                                                                                                              |
| LastLedgerSequence | number           | Highest ledger index this transaction can appear in. Specifying this field places a strict upper limit on how long the transaction can wait to be validated or rejected.                                                                                                            |
| Memos              | array of objects | This contains an object as follows,<br><br>![image.png](./Assets/Images/acquire-submit-lease-success-memo.png)<br><br> The field values are in hex format. If decoded, 'MemoType' has 'evnAcquireLease' and 'MemoFormat' has 'base64' as values. 'MemoData' has a string in base64. |
| NFTokenSellOffer   | string           | The index of the sell-offer that was bought through this transaction.                                                                                                                                                                                                               |
| Sequence           | number           | The sequence number of the account sending the transaction. A transaction is only valid if the Sequence number is exactly 1 greater than the previous transaction from the same account.                                                                                            |
| SigningPubKey      | string           | Hex representation of the public key that corresponds to the private key used to sign this transaction. If an empty string, indicates a multi-signature is present in the Signers field instead.                                                                                    |
| TransactionType    | string           | The type of transaction. In this situation, it is always _'NFTokenAcceptOffer'_                                                                                                                                                                                                     |
| TxnSignature       | string           | (Automatically added when signing) The signature that verifies this transaction as originating from the account it says it is from.                                                                                                                                                 |
| date               | number           | A number of seconds since January 1, 2000 (00:00 UTC) indicating the close time of the ledger in which the transaction was applied. This value does not have a precise relationship with physical time, and is dependent on the close time resolution.                              |
| hash               | string           | The SHA-512 hash of the transaction.                                                                                                                                                                                                                                                |
| inLedger           | number           | (Deprecated) Alias for ledger_index.                                                                                                                                                                                                                                                |
| ledger_index       | number           | The ledger index of the ledger that includes this transaction.                                                                                                                                                                                                                      |
| validated          | boolean          | Whether the transaction is validated or not.                                                                                                                                                                                                                                        |
| ip                 | string           | The public IP address of the host server.                                                                                                                                                                                                                                           |
| contract_id        | string           | The unique contract identifier.                                                                                                                                                                                                                                                     |
| peer_port          | string           | The port used for the communication among the instances in the cluster.                                                                                                                                                                                                             |
| user_port          | string           | The port used for the communication between the user and the instance.                                                                                                                                                                                                              |
| acquireRefId       | string           | The hash of the transaction that requested the initial acquiring of the instance.                                                                                                                                                                                                   |

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

### Example

```javascript
const response = await watchAcquireResponse(tx);
```

<br>

## Extend Lease - extendLease(hostAddress, moments, instanceName, options = {}) => "`async extendLease(hostAddress, moments, instanceName, options = {})`"

This function is called by a tenant client to extend an available instance in certain host. This function can take four parameters as follows.

### Parameters

| Name         | Type   | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| hostAddress  | string | HostAddress is a cryptographic code that allows a user to receive cryptocurrencies. <br><br> example: "rKtuh3pGwkPk86BuVrPNS58JkyMuz79DbB"                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| moments      | number | Moment is 1190 ledgers (est. 1 hour).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| instanceName | string | Tenant received instance name. this name can be retrieve by performing [acquire Lease](#acquire-lease---async-acquireleasehostaddress-requirement-options).                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| options      | object | This is an optional field and contains necessary details for the transactions. The attributes are explained down below. <br><br> **transactionOptions** - During the extending lease process, a [Payment](https://xrpl.org/payment.html#payment) transaction takes place. Therefore the fields defined in the official XRPL documentation for the Payment transaction can be specified within this object. <br><br> **timeout** - This specifies a timeout for the transaction to be completed. it accepts a number and time in milliseconds. this is optional and defaults to 60000 unless provided. |

### Responce Format

An example of a successful response:

```
exampleResponce {
  transaction: {
    LedgerHash: 'A4C1F8FB2F34F03B3621CF4418857977921B5B38B2C05E456499399BED938045',
    LedgerIndex: 6316452,
    Account: 'rGhvPCX1x2qyUTEEGQNneVYwgwXWYCvp8P',
    Amount: '1',
    Destination: 'rELWA2WYB2kW9RyZUnx7NAEUrQsaFwWrLc',
    Fee: '20',
    Flags: 0,
    LastLedgerSequence: 6316461,
    Memos: [
    { type: 'evnExtendSuccess', format: 'hex', data: '000014F7' },
    {
      type: 'evnExtendRef',
      format: 'hex',
      data: '336086A8DB9D2C6E6EE7CA5A31CC4A999102E6556E0891D44B6580FFFDEFF443'
    }
    ],
    Sequence: 3882879,
    SigningPubKey: '03A8141293AEEE6C44CA7D80095042234924009FA2B3FA8E693D99A5069BACA37C',
    TransactionType: 'Payment',
    TxnSignature: '304502210098A02075B003CDA3847F62AE8A0F95C59C1CED6DB54006A41A919781A5BBD26B02203BCBBFEF27821ECA96C7065129EC030011428253BC9B7F0AC5585AE430251A84',
    date: 717651611,
    hash: 'F95B95574CAE971C5D5019B04C1B0686F7EF6D12E8D2502E91A42437131A6292'
  },
  expiryMoment: 5310,
  extendeRefId: 'E8F3D629854A2E2F863F9D0C347158DB5380397AED56982623EB2E10D5E1B1C7'
}
```

Now let's see the details of the response fields.

| Name               | Type             | Description                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| LedgerHash         | string           | The LedgerHashes object type contains a history of prior ledgers that led up to this ledger version, in the form of their hashes. Objects of this ledger type are modified automatically when closing a ledger. The LedgerHashes objects exist to make it possible to look up a previous ledger's hash with only the current ledger version and at most one lookup of a previous ledger version. |
| LedgerIndex        | number           | A ledger index is a 32-bit unsigned integer used to identify a ledger. The ledger index is sometimes known as the ledger's sequence number. The very first ledger was ledger index 1, and each new ledger has a ledger index that is 1 higher than the ledger index of the ledger immediately before it.                                                                                         |
| Account            | string           | The unique address of the [account](https://xrpl.org/accounts.html) that initiated the transaction.                                                                                                                                                                                                                                                                                              |
| Amount             | string           | To specify an amount of XRP, use a String Number indicating drops of XRP, where each drop is equal to 0.000001 XRP. <br><br>For example, to specify 13.1 XRP : "13100000"                                                                                                                                                                                                                        |
| Destination        | string           | Tenant xrpl address that made the extend lease request.                                                                                                                                                                                                                                                                                                                                          |
| Fee                | string           | Integer amount of Evers, in drops, to be destroyed as a cost for distributing this transaction to the network. Some transaction types have different minimum requirements.                                                                                                                                                                                                                       |
| Flags              | number           | Set of bit-flags for this transaction.                                                                                                                                                                                                                                                                                                                                                           |
| LastLedgerSequence | number           | Highest ledger index this transaction can appear in. Specifying this field places a strict upper limit on how long the transaction can wait to be validated or rejected.                                                                                                                                                                                                                         |
| Memos              | array of objects | Additional arbitrary information used to identify this transaction.                                                                                                                                                                                                                                                                                                                              |
| Sequence           | number           | The sequence number of the account sending the transaction. A transaction is only valid if the Sequence number is exactly 1 greater than the previous transaction from the same account.                                                                                                                                                                                                         |
| SigningPubKey      | string           | Hex representation of the public key that corresponds to the private key used to sign this transaction. If an empty string, indicates a multi-signature is present in the Signers field instead.                                                                                                                                                                                                 |
| TransactionType    | string           | The type of transaction. Valid types include: Payment                                                                                                                                                                                                                                                                                                                                            |
| TxnSignature       | string           | (Automatically added when signing) The signature that verifies this transaction as originating from the account it says it is from.                                                                                                                                                                                                                                                              |
| date               | number           | A number of seconds since January 1, 2000 (00:00 UTC) indicating the close time of the ledger in which the transaction was applied. This value does not have a precise relationship with physical time, and is dependent on the close time resolution.                                                                                                                                           |
| hash               | string           | Every signed transaction has a unique "hash" that identifies it. The server provides the hash in the response when you submit the transaction.                                                                                                                                                                                                                                                   |
| expiryMoment       | number           | Defines which moment instance going to expire.                                                                                                                                                                                                                                                                                                                                                   |
| extendeRefId       | string           | Transaction ID of the extend request.                                                                                                                                                                                                                                                                                                                                                            |

### Example

```javascript
const result = await tenant.extendLease(hostAddress, moments, instanceName, {
  timeout: timeout,
});
```

<br>

## Extend Lease Submit - extendLeaseSubmit(hostAddress, amount, tokenID, options = {}) => "`async extendLeaseSubmit(hostAddress, amount, tokenID, options = {})`"

This function is called by a tenant client to submit the extend lease transaction in certain host. This function will be called inside extendLease function. This function can take four parameters as follows.

| Name        | Type   | Description                                                                                                                                                                                                                                                                                                                                                                                                |
| ----------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| hostAddress | string | HostAddress is a cryptographic code that allows a user to receive cryptocurrencies. <br><br> example: "rKtuh3pGwkPk86BuVrPNS58JkyMuz79DbB"                                                                                                                                                                                                                                                                 |
| moments     | number | Moment is 1190 ledgers (est. 1 hour).                                                                                                                                                                                                                                                                                                                                                                      |
| tokenID     | string | Tenant received instance name. this name can be retrieve by performing [acquire Lease](#acquire-lease---async-acquireleasehostaddress-requirement-options).                                                                                                                                                                                                                                                |
| options     | object | This is an optional field and contains necessary details for the transactions. The attributes are explained down below. <br><br> **transactionOptions** - During the extending lease process, a [Payment](https://xrpl.org/payment.html#payment) transaction takes place. Therefore the fields defined in the official XRPL documentation for the Payment transaction can be specified within this object. |

## Responce Format

An example of a successful response:

```
exampleResponce  {
  id: '753EC1763AF94FCAC17680FE712B4C962A61E4C9C36B5E791D5F223B8574FF16',
  code: 'tesSUCCESS',
  details: {
    Account: 'refZidLPZYfppNnE6HigQf6shGPmZgnmH',
    Amount: {
      currency: 'EVR',
      issuer: 'rfxLPXCcSmwR99dV97yFozBzrzpvCa2VCf',
      value: '4'
    },
    Destination: 'rGhvPCX1x2qyUTEEGQNneVYwgwXWYCvp8P',
    Fee: '20',
    Flags: 0,
    LastLedgerSequence: 6326305,
    Memos: [ {
    Memo: {
      MemoData: '00010000A547B3F85414A33C2AB38F7788E42AC0F4D2734701ADB787000001EC',
      MemoFormat: '686578',
      MemoType: '65766E457874656E644C65617365'
    }
    } ],
    Sequence: 6325615,
    SigningPubKey: '03A0C88F5A9410F00AD317AEFF28ADAE1CCB5188F43AC45E34A39AE1470BBAEEF3',
    TransactionType: 'Payment',
    TxnSignature: '3044022048D9B213B28C615A0B0C5494FC3380575862665C87593447E4D9731C0F638AB5022046B1927FDF93E9F0EF8C54D1641272E3271727B4B1BB77E6B37161AA63C116D9',
    date: 717681453,
    hash: '753EC1763AF94FCAC17680FE712B4C962A61E4C9C36B5E791D5F223B8574FF16',
    inLedger: 6326297,
    ledger_index: 6326297,
    meta: {
      AffectedNodes: [Array],
      TransactionIndex: 0,
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
| Fee                | string           | Integer amount of Evers, in drops, to be destroyed as a cost for distributing this transaction to the network. Some transaction types have different minimum requirements.                                                                             |
| Flags              | number           | Set of bit-flags for this transaction.                                                                                                                                                                                                                 |
| LastLedgerSequence | number           | Highest ledger index this transaction can appear in. Specifying this field places a strict upper limit on how long the transaction can wait to be validated or rejected.                                                                               |
| Memos              | array of objects | Additional arbitrary information used to identify this transaction.                                                                                                                                                                                    |
| Sequence           | number           | The sequence number of the account sending the transaction. A transaction is only valid if the Sequence number is exactly 1 greater than the previous transaction from the same account.                                                               |
| SigningPubKey      | string           | Hex representation of the public key that corresponds to the private key used to sign this transaction. If an empty string, indicates a multi-signature is present in the Signers field instead.                                                       |
| TransactionType    | string           | The type of transaction. Valid types include: Payment                                                                                                                                                                                                  |
| TxnSignature       | string           | (Automatically added when signing) The signature that verifies this transaction as originating from the account it says it is from.                                                                                                                    |
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

## Watch Extend Response - watchExtendResponse(tx, options = {}) => "`async watchExtendResponse(tx, options = {})`"

This function watches for an extendlease-success response(transaction) and returns the response or throws the error response on extendlease-error response from the host XRPL account. This function is called within the [_extendLease_](#extend-lease---async-acquireleasehostaddress-requirement-options---) function.

### Parameters

| Name    | Type   | Description                                                                                                                                                                                                                                                                                                       |
| ------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| tx      | object | Response of [extendLeaseSubmit](#extend-lease-submit)                                                                                                                                                                                                                                                             |
| options | object | This is an optional field and contains necessary details for the transactions. The attributes are explained down below. <br><br>**timeout** - This specifies a timeout for the transaction to be completed. it accepts a number and time in milliseconds. this is optional and defaults to 60000 unless provided. |

### Responce Format

An example of a successful response:

```
exampleResponse {
  transaction: {
    LedgerHash: 'FC7705C15B69B5CB716E26A146FA60B297824ABA2E5B7778C24D18D8DD8B2F82',
    LedgerIndex: 6352437,
    Account: 'rGhvPCX1x2qyUTEEGQNneVYwgwXWYCvp8P',
    Amount: '1',
    Destination: 'rMXDvDMBEJ1GwYwz1qexgfKXwqLt1W9DLU',
    Fee: '20',
    Flags: 0,
    LastLedgerSequence: 6352446,
    Memos: [
    { type: 'evnExtendSuccess', format: 'hex', data: '000014FD' },
    {
      type: 'evnExtendRef',
      format: 'hex',
      data: '88D63288A5D49E3438BD6594CE81BA0B00DA6FD3CE3BA2B12CEA1F04D402614C'
    }
    ],
    Sequence: 3882985,
    SigningPubKey: '03A8141293AEEE6C44CA7D80095042234924009FA2B3FA8E693D99A5069BACA37C',
    TransactionType: 'Payment',
    TxnSignature: '304402200EB7CA103281017C26B29743E35BAA82EFD0B35483CB3C7AF6BC3CB33B2228D102203A0786EEE995C8AD397F753295547335EBE38B22B5DED4B5CF68642B4E228ADE',
    date: 717760851,
    hash: 'FB6B329FBC8F3B634DBC69F1F2AFE2086C68E5BBC18933CE3D59257D746EC3F9'
  },
  expiryMoment: 5341
}
```

Now let's see the details of the response fields.

| Name               | Type             | Description                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| LedgerHash         | string           | The LedgerHashes object type contains a history of prior ledgers that led up to this ledger version, in the form of their hashes. Objects of this ledger type are modified automatically when closing a ledger. The LedgerHashes objects exist to make it possible to look up a previous ledger's hash with only the current ledger version and at most one lookup of a previous ledger version. |
| LedgerIndex        | number           | A ledger index is a 32-bit unsigned integer used to identify a ledger. The ledger index is sometimes known as the ledger's sequence number. The very first ledger was ledger index 1, and each new ledger has a ledger index that is 1 higher than the ledger index of the ledger immediately before it.                                                                                         |
| Account            | string           | The unique address of the [account](https://xrpl.org/accounts.html) that initiated the transaction.                                                                                                                                                                                                                                                                                              |
| Amount             | string           | To specify an amount of XRP, use a String Number indicating drops of XRP, where each drop is equal to 0.000001 XRP. <br><br>For example, to specify 13.1 XRP : "13100000"                                                                                                                                                                                                                        |
| Destination        | string           | Tenant xrpl address that made the extend lease request.                                                                                                                                                                                                                                                                                                                                          |
| Fee                | string           | Integer amount of Evers, in drops, to be destroyed as a cost for distributing this transaction to the network. Some transaction types have different minimum requirements.                                                                                                                                                                                                                       |
| Flags              | number           | Set of bit-flags for this transaction.                                                                                                                                                                                                                                                                                                                                                           |
| LastLedgerSequence | number           | Highest ledger index this transaction can appear in. Specifying this field places a strict upper limit on how long the transaction can wait to be validated or rejected.                                                                                                                                                                                                                         |
| Memos              | array of objects | Additional arbitrary information used to identify this transaction.                                                                                                                                                                                                                                                                                                                              |
| Sequence           | number           | The sequence number of the account sending the transaction. A transaction is only valid if the Sequence number is exactly 1 greater than the previous transaction from the same account.                                                                                                                                                                                                         |
| SigningPubKey      | string           | Hex representation of the public key that corresponds to the private key used to sign this transaction. If an empty string, indicates a multi-signature is present in the Signers field instead.                                                                                                                                                                                                 |
| TransactionType    | string           | The type of transaction. Valid types include: Payment                                                                                                                                                                                                                                                                                                                                            |
| TxnSignature       | string           | (Automatically added when signing) The signature that verifies this transaction as originating from the account it says it is from.                                                                                                                                                                                                                                                              |
| date               | number           | A number of seconds since January 1, 2000 (00:00 UTC) indicating the close time of the ledger in which the transaction was applied. This value does not have a precise relationship with physical time, and is dependent on the close time resolution.                                                                                                                                           |
| hash               | string           | Every signed transaction has a unique "hash" that identifies it. The server provides the hash in the response when you submit the transaction.                                                                                                                                                                                                                                                   |
| expiryMoment       | number           | Defines which moment instance going to expire.                                                                                                                                                                                                                                                                                                                                                   |

## Example

```javascript
const response = await this.watchExtendResponse(tx, options);
```
