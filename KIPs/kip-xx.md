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

Since the introduction of [EIP-2028](https://eips.ethereum.org/EIPS/eip-2028), several bundler software solutions have been developed.

The eth_estimateUserOperationGas API calculates the gas required for a userOperation. This estimate is computed based on the input’s composition, which includes both nonzero and zero bytes. Following the Istanbul hardfork, Ethereum set the gas cost at 16 gas per nonzero byte and 4 gas per zero byte. As a result, some bundlers have adopted predefined gas costs for these byte types.

To maintain compatibility with Ethereum's tooling ecosystem, the Kaia mainnet aligns its gas pricing with the specifications of EIP-2028 introduced in the Prague hardfork, which defines new costs for nonzero and zero bytes.

## Specification

The gas cost distinguishes between non-zero and zero bytes, assigning 16 and 4 gas units to each, respectively.

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
