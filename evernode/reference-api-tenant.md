# Tenant Client

## extendLease(hostAddress, moments, instanceName, options = {}) => "`async extendLease(hostAddress, moments, instanceName, options = {})`"

This function is called by a tenant client to extend an available instance in certain host. This function can take four parameters as follows.

### Parameters

| Name         | Type   | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------ | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| hostAddress  | string | HostAddress is a cryptographic code that allows a user to receive cryptocurrencies. <br><br> example: "rKtuh3pGwkPk86BuVrPNS58JkyMuz79DbB"                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| moments      | number | Moment is 1190 ledgers (est. 1 hour).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| instanceName | string | Tenant received instance name. this name can be retrieve by performing [acquire Lease](#acquire-lease).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| options      | object | This is an optional field and contains necessary details for the transactions. The attributes are explained down below. <br><br> **transactionOptions** - During the extending lease process, a [Payment](https://xrpl.org/payment.html#payment) transaction takes place. Therefore the fields defined in the official XRPL documentation for the Payment transaction can be specified within this object. <br><br> **timeout** - This specifies a timeout for the transaction to be completed. it accepts a number and time in miliseconds. this is optional and defaults to 60000 unless provided. |

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

# extendLeaseSubmit(hostAddress, amount, tokenID, options = {}) => "`async extendLeaseSubmit(hostAddress, amount, tokenID, options = {})`"

This function is called by a tenant client to submit the extend lease transaction in certain host. This function will be called inside extendLease function. This function can take four parameters as follows.

| Name        | Type   | Description                                                                                                                                                                                                                                                                                                                                                                                                |
| ----------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| hostAddress | string | HostAddress is a cryptographic code that allows a user to receive cryptocurrencies. <br><br> example: "rKtuh3pGwkPk86BuVrPNS58JkyMuz79DbB"                                                                                                                                                                                                                                                                 |
| moments     | number | Moment is 1190 ledgers (est. 1 hour).                                                                                                                                                                                                                                                                                                                                                                      |
| tokenID     | string | Tenant received instance name. this name can be retrieve by performing [acquire Lease](#acquire-lease).                                                                                                                                                                                                                                                                                                    |
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

# watchExtendResponse(tx, options = {}) => "`async watchExtendResponse(tx, options = {})`"

This function watches for an extendlease-success response(transaction) and returns the response or throws the error response on extendlease-error response from the host XRPL account. This function is called within the [_extendLease_](#extend-lease---async-acquireleasehostaddress-requirement-options---) function.

### Parameters

| Name    | Type   | Description                                                                                                                                                                                                                                                                                                      |
| ------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| tx      | object | Response of [extendLeaseSubmit](#extend-lease-submit)                                                                                                                                                                                                                                                            |
| options | object | This is an optional field and contains necessary details for the transactions. The attributes are explained down below. <br><br>**timeout** - This specifies a timeout for the transaction to be completed. it accepts a number and time in miliseconds. this is optional and defaults to 60000 unless provided. |

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
