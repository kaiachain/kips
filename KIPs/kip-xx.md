---
kip: to be assigned
title: Transaction data gas cost reduction
author: Lake (@hyunsooda) and Ollie (@blukat29)
discussions-to: https://github.com/klaytn/kips/issues/163 # TODO: Revise me
status: Draft
type: Standards Track
category: Core
created: 2024-10-18
---

## Simple Summary

Changing to calldata gas cost.

## Abstract

This KIP introduces a new tag for calldata gas payment, defining a calculation method that distinguishes between zero and non-zero bytes, with distinct costs assigned to each byte.

## Motivation

TBD

## Specification

The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in RFC 2119.

The gas cost distinguishes between non-zero and zero bytes, assigning 16 and 4 gas units to each, respectively.

### Overview

Since the genesis block, calldata gas calculation has followed two branches:

- Non-zero bytes: 68 gas units per byte
- Zero bytes: 4 gas units per byte
With the Istanbul hardfork, the gas calculation was modified to no longer distinguish between zero and non-zero bytes, introducing a flat rate of 100 gas units per byte.

The Prague hardfork reverted to the original logic from the genesis block, reintroducing the distinction between non-zero and zero bytes. However, the assigned costs were adjusted to 16 gas units per non-zero byte and 4 gas units per zero byte.

In summary, these changes result in a reduction of 84 gas units per non-zero byte and 96 gas units per zero byte.

## Backward Compatibility

Since the Kaia client implementation applies different logic based on the hardfork state, the correct gas cost is determined according to the block number associated with each hardfork.

## References
[EIP-2028](https://eips.ethereum.org/EIPS/eip-2028)

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
