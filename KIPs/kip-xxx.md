---
kip: TBD
title: SetCode for EOA
author: Ollie (@blukat29), Garry (@Mdaiki0730), Shiki (@shiki-tak)
discussions-to: TBD
status: Draft
type: Standards Track
category: Core
created: 2024-11-15
---

## Abstract

Add a new transaction type to assign a code to an EOA. This KIP focuses on describing the difference from [EIP-7702](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7702.md).

## Motivation

TBU

## Specification

### Parameters

| Parameter | Value |
|-|-|
| `EMPTY_CODE_HASH` | `0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470` |
| `SET_CODE_TX_TYPE` | `0x04` |
| `ENVELOPED_SET_CODE_TX_TYPE` | `0x7804` |

### SetCode Transaction

Introduce the SetCode transaction type where its RLP encoding is defined below.

```
0x7804 || rlp([chain_id, nonce, max_priority_fee_per_gas, max_fee_per_gas, gas_limit, destination, value, data, access_list, authorization_list, signature_y_parity, signature_r, signature_s])

authorization_list = [[chain_id, address, nonce, y_parity, r, s], ...]
```

The fields follow the same semantics as EIP-7702.

Its consensus type prefix (that affects `block.transactionRoot`) is `ENVELOPED_SET_CODE_TX_TYPE (0x7804)` and its eth namespace API type prefix is `SET_CODE_TX_TYPE (0x04)`. See “Changes to API” for clarification.

#### Behavior

The transaction’s behavior follows that of EIP-7702 except for the following:

- For each authorization tuple `[chain_id, address, nonce, y_parity, r, s]`, fetch the AccontKey field of the recovered authority account.
- Verify that the AccountKey is of type `AccountKeyTypeLegacy`. If verification fails, immediately stop processing the tuple and continue to the next tuple in the list.

#### Delegation Designation

Semantics of `EXTCODESIZE`, `EXTCODECOPY`, `EXTCODEHASH`, `CALL`, `CALLCODE`, `STATICCALL`, `DELEGATECALL` opcodes follows EIP-7702.

#### Gas costs

The intrinsic gas cost of a SetCode transaction follows the EIP-7702, meaning it inherits [EIP-2930](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2930.md) and [EIP-2028](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2028.md). Note that the EIP-2028 rule is realized by by KIP-xxx.

#### Transaction Origination

Since Kaia hasn't been explicitly enforcing [EIP-3607](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-3607.md), there is no need to modify transaction origin restriction.

Do follow the EIP-7702’s behavior where if a transaction’s destination has a delegation, also warm its target

### Account State

The account RLP encoding for the world state trie needs to change.

```
EOA's compact encoding: 0x01 || rlp([nonce, balance, humanReadable, accountKey])
EOA's full encoding:    0x01 || rlp([[nonce, balance, humanReadable, accountKey], storageRoot, codeHash, codeInfo])
SCA's encoding:         0x02 || rlp([[nonce, balance, humanReadable, accountKey], storageRoot, codeHash, codeInfo])
```

Now both EOAs and SCAs hold the same set of 7 fields: `nonce`, `balance`, `humanReadable`, `accountKey`, `storageRoot`, `codeHash`, and `codeInfo`.

When an EOA is RLP encoded, the compact encoding is used when the account has storageRoot = 0x0, codeHash = EMPTY_CODE_HASH, and codeInfo = 0x0. Conversely, when decoding an RLP-encoded EOA, the compact encoding implies that the account has storageRoot = 0x0, codeHash = EMPTY_CODE_HASH, and codeInfo = 0x0.

If an EOA’s delegation was cleared by a SetCode transaction with authorization.address = 0x0, set its CodeHash to EMPTY_CODE_HASH as per EIP-7702, and also set its CodeInfo to 0x0. Note that the account does not necessarily return to compact encoding. It will be compactly encoded only if storageRoot is 0x0.

An EOA with codeHash = EMPTY_CODE_HASH can be called “EOA without code” and an EOA with codeHash != EMPTY_CODE_HASH can be called “EOA with code”. Note that EOA without code doesn’t always mean it will be compactly encoded.

### Interaction with other transaction types

The `to` address of the following transaction types MUST be an EOA without code:

- `TxTypeValueTransfer`
- `TxTypeFeeDelegatedValueTransfer`
- `TxTypeFeeDelegatedValueTransferWithRatio`
- `TxTypeValueTransferMemo`
- `TxTypeFeeDelegatedValueTransferMemo`
- `TxTypeFeeDelegatedValueTransferMemoWithRatio`

The `from` address of the following transaction types MUST be an EOA without code:

- `TxTypeAccountUpdate`
- `TxTypeFeeDelegatedAccountUpdate`
- `TxTypeFeeDelegatedAccountUpdateWithRatio`

The `to` address of the following transaction types MUST be an EOA with code or an SCA:

- `TxTypeSmartContractExecution`
- `TxTypeFeeDelegatedSmartContractExecution`
- `TxTypeFeeDelegatedSmartContractExecutionWithRatio`

Note that whether the storageRoot is 0x0 or not does not matter in transaction type restrictions.

### Changes to APIs

#### eth_getRawTransaction, eth_sendRawTransaction

eth_getRawTransaction returns the raw transaction RLP prefixed with `SET_CODE_TX_TYPE (0x04)` and eth_sendRawTransaction accepts such raw transaction.

#### kaia_getRawTransaction, kaia_sendRawTransaction

kaia_getRawTransaction returns the raw transaction RLP prefixed with `ENVELOPED_SET_CODE_TX_TYPE (0x7804)` and kaia_sendRawTransaction accepts such raw transaction.

#### eth_getBlock*, eth_getTransaction*, eth_getTransactionReceipt

For SetCode transaction type, eth namespace APIs that return the transaction fields MUST show the `authorizationList` field and its `type: "0x04"`.

#### kaia_getBlock*, kaia_getTransaction*, kaia_getTransactionReceipt

For SetCode transaction type, kaia namespace APIs that return the transaction fields MUST show the `authorizationList` field and its `typeInt: 30724`, and `type: "TxTypeEthereumSetCode"`.

#### eth_getCode, eth_getStorageAt, kaia_getCode, kaia_getStorageAt

APIs querying the code and storage of an account MUST work correctly for EOA if its storageRoot is not 0x0.

#### kaia_getAccount

For both EOAs and SCAs, its kaia_getAccount result SHOULD include storageRoot, codeHash, codeFormat, vmVersion fields.

### Changes to SDK

Before an SDK signs an authorization tuple, the SDK MAY verify that the authority’s AccountKey is of type AccountKeyTypeLegacy. If the SDK does not verify, it is the responsibility of the SDK user (e.g. Wallet) to make sure the authority is eligible to use the SetCode transaction.

## Rationale

### Extending the EOA instead of converting to SCA

Because the feature is called “Set EOA account code”, there could be a need to distinguish between EOA with code and an SCA.

### EOAs with updated AccountKeys cannot sign authorization

In Kaia, EOAs may change the transaction signing permissions by sending an AccountUpdate transaction. But in this KIP, we strictly let the original private key (i.e. the one that AccountKeyLegacy accepts) to sign the SetCode authorization. Decoupling the two permissions will be confusing, so EOAs with code must have AccountKeyLegacy at all times.

### EOA RLP formats depend on field values

For backward compatibility, we have to retain the compact encoding.

If we choose to select the encoding depending on hardfork instead, then all trie handling code must be fork-aware, and coding in that is non-trivial.

### Kaia transaction type restrictions

ValueTransfer transaction types cannot be sent to EOAs with code. Kaia’s ValueTransfer transaction types have the semantics of simply increasing the balance of the destination EOA. If ValueTransfer can target EOA with code, the balance increase would bypass the EOA’s fallback() or receive() function. Opening a way to bypass the fallback may have an unknown security implication, thus prohibiting such a case.

AccountUpdate transaction types cannot be sent to EOAs with code. The reason is the same as why EOAs with updated AccountKeys cannot sign authorization.

SmartContractExecution transaction types can be sent to EOAs with code. Kaia’s SmartContractExecution transaction types had the semantics of executing the code of the destination SCA. Now that EOAs with code has a code, SmartContractExecution transactions indeed can invoke them.

## Backwards Compatibility

The stateRoot calculation is backward compatible because we retain the EOA’s compact encoding.

## Implementation

[draft implementation](https://github.com/kaiachain/kaia/tree/feat/eip-7702)

## References

- [EIP-7702](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7702.md)

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).

