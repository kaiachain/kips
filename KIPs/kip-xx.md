---
kip: to be assigned
title: Transaction data gas cost reduction
author: Lake (@hyunsooda) and Ollie (@blukat29)
discussions-to: https://github.com/kaiachain/kips/issues/22
status: Draft
type: Standards Track
category: Core
created: 2024-10-18
---

## Abstract

Reduce the calldata gas costs to the [EIP-2028](https://eips.ethereum.org/EIPS/eip-2028) level. The purpose is to achieve better compatibility with Ethereum-oriented SDKs and tools.

## Motivation

The adoption of [EIP-2028](https://eips.ethereum.org/EIPS/eip-2028) will improve the compatibility with [ERC-4337](https://eips.ethereum.org/EIPS/eip-4337) ecosystem tools.
Since the introduction of [ERC-4337](https://eips.ethereum.org/EIPS/eip-4337), several bundler software solutions have been developed.
The bundler's `eth_estimateUserOperationGas` API calculates the gas required for a UserOperation.
The calculation depends on the calldata costs in a UserOperation.
The issue is that some tools assumes EIP-2028 rule [unless explicitly configured](https://github.com/eth-infinitism/bundler/blob/f4647969386aa859b7edf608467168d140e5f92c/packages/sdk/src/PreVerificationGasCalculator.ts#L44-L54), making their result incompatible with Kaia.
This proposal introduces a new hardfork that aligns the calldata cost calculation to that of EIP-2028.

## Specification

The gas cost for calldata is determined by the number of non-zero and zero bytes, with each non-zero byte costing 16 gas units and each zero byte costing 4 gas units.

### Overview

The calldata costs has been depending on the transaction types and hardfork levels.

Since the genesis block, calldata gas calculation was calculated using the formula:  
Transaction type 0 (Legacy):
`nonzero_bytes_in_calldata` * 68 + `zero_bytes_in_calldata` * 4  
Other types:
`nonzero_bytes_in_calldata` * 100 + `zero_bytes_in_calldata` * 100

Since the Istanbul hardfork, the calculation was simplified to the latter:  
For all transaction types: `nonzero_bytes_in_calldata` * 100 + `zero_bytes_in_calldata` * 100  

This proposal introduces a new hardfork in which the formula will be:  
For all transaction types: `nonzero_bytes_in_calldata` * 16 + `zero_bytes_in_calldata` * 4  
which is identical to [EIP-2028](https://eips.ethereum.org/EIPS/eip-2028).

## Backward Compatibility

The gas pricing change is backwards incompatible since the hardfork.

SDKs and Wallets will be able to continue operating with no change as they usually use `eth_estimateGas` API.

## References
[EIP-2028](https://eips.ethereum.org/EIPS/eip-2028)

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
