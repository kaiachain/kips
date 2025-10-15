---
kip: xxx
title: eth_config JSON-RPC Method
author: Garry (@Mdaiki0730), Ollie (@blukat29)
discussions-to: TBD
status: Draft
type: Core
created: 2025-10-15
---

## Abstract

Add a new api endpoint to fetch the configuration for each fork. This KIP focuses on describing the difference from [EIP-7910](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7910.md).

## Motivation

Increases transparency of configuration values ​​for each fork by adding an `eth_config` api endpoint.
This API will allow users to check that their node are ready for the next fork and that the current fork configuration values ​​are correct.

## Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174.

All clients constraints directly under the specification follow [EIP-7910](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7910.md).

### Configuration RPC

The Configuration RPC follows that of [EIP-7910](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7910.md).

### Result Object Structure

The RPC result object structure follows [EIP-7910](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7910.md).

### Members of the Configuration Object

The menbers of the configuration object follows that of [EIP-7910](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7910.md) except for the following fields.

#### `activationTime`

Kaia does not use timestamps for fork management. 
This is considered to have no suitable return value and will exclude the field.
From the JS SDK, `result.current.activationTime === undefined`. And `undefined` will explicitly throw an error, so users can know that the field is absent.

#### `blobSchedule`

Kaia does not implement [EIP-4844](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-4844.md), so blobSchedule will always be `null`.

#### `forkId`

The forkId follows that of [EIP-7910](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7910.md).
Kaia does not follow [EIP-6122](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-6122.md), but only inherits it in the `fork_hash` spec.

#### `precompiles`

The precompiles follows that of [EIP-7910](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7910.md).

Additionally, as of cancun there are Kaia's own precompiles.
The contract names are (in order): `VMLOG`, `FEE_PAYER`, `VALIDATE_SENDER`

If a new original precompile is added, the new “precompile KIP” shall declare its name.

#### `systemContracts`

A JSON object representing the system-level contracts defined by KIPs.
These have keys that are alphabetically sorted contract names (e.g., ADDRESS_BOOK) and values ​​that are 20-byte addresses.

The system contracts from genesis are (in order) `MAINNET_CREDIT`, `ADDRESS_BOOK`.

For Randao, the only system contract is `REGISTRY`.

For Prague, the only system contract is `HISTORY_STORAGE_ADDRESS`.

Future forks MUST define the list of system contracts in their meta-KIPs.

### Blob Parameter Only Forks

Blob Parameter Only Forks do not exist in kaia, so this is ignored.

## Rationale

### Other system contracts

Kaia's `eth_config` will return a subset of the system contracts listed [kaia doc](https://docs.kaia.io/references/contract-addresses/).
The common feature of these is that they are associated with a fork.
Other system contracts listed are registered in the registry and can be obtained by calling it.

## Backwards Compatibility

This does not affect the backward compatibility because this does not involve hardfork.

## Test Cases

### Sample Configs

Kairos Prague Config

```JSON
{
  "blobSchedule": null,
  "chainId": "0x3E9",
  "forkId": "0x0929e24e",
  "precompiles": {
    "BLAKE2F": "0x0000000000000000000000000000000000000009",
    "BLS12_G1ADD": "0x000000000000000000000000000000000000000b",
    "BLS12_G1MSM": "0x000000000000000000000000000000000000000c",
    "BLS12_G2ADD": "0x000000000000000000000000000000000000000d",
    "BLS12_G2MSM": "0x000000000000000000000000000000000000000e",
    "BLS12_MAP_FP2_TO_G2": "0x0000000000000000000000000000000000000011",
    "BLS12_MAP_FP_TO_G1": "0x0000000000000000000000000000000000000010",
    "BLS12_PAIRING_CHECK": "0x000000000000000000000000000000000000000f",
    "BN254_ADD": "0x0000000000000000000000000000000000000006",
    "BN254_MUL": "0x0000000000000000000000000000000000000007",
    "BN254_PAIRING": "0x0000000000000000000000000000000000000008",
    "ECREC": "0x0000000000000000000000000000000000000001",
    "FEE_PAYER": "0x00000000000000000000000000000000000003fe",
    "ID": "0x0000000000000000000000000000000000000004",
    "KZG_POINT_EVALUATION": "0x000000000000000000000000000000000000000a",
    "MODEXP": "0x0000000000000000000000000000000000000005",
    "RIPEMD160": "0x0000000000000000000000000000000000000003",
    "SHA256": "0x0000000000000000000000000000000000000002",
    "VALIDATE_SENDER": "0x00000000000000000000000000000000000003ff",
    "VMLOG": "0x00000000000000000000000000000000000003fd"
  },
  "systemContracts": {
    "ADDRESS_BOOK": "0x0000000000000000000000000000000000000400",
    "HISTORY_STORAGE_ADDRESS": "0x0000f90827f1c53a10cb7a02335b175320002935",
    "MAINNET_CREDIT": "0x0000000000000000000000000000000000000000",
    "REGISTRY": "0x0000000000000000000000000000000000000401"
  }
}
```

Kairos Cancun Config

```JSON
{
  "blobSchedule": null,
  "chainId": "0x3E9",
  "forkId": "0xbef71d30",
  "precompiles": {
    "BLAKE2F": "0x0000000000000000000000000000000000000009",
    "BN254_ADD": "0x0000000000000000000000000000000000000006",
    "BN254_MUL": "0x0000000000000000000000000000000000000007",
    "BN254_PAIRING": "0x0000000000000000000000000000000000000008",
    "ECREC": "0x0000000000000000000000000000000000000001",
    "FEE_PAYER": "0x00000000000000000000000000000000000003fe",
    "ID": "0x0000000000000000000000000000000000000004",
    "KZG_POINT_EVALUATION": "0x000000000000000000000000000000000000000a",
    "MODEXP": "0x0000000000000000000000000000000000000005",
    "RIPEMD160": "0x0000000000000000000000000000000000000003",
    "SHA256": "0x0000000000000000000000000000000000000002",
    "VALIDATE_SENDER": "0x00000000000000000000000000000000000003ff",
    "VMLOG": "0x00000000000000000000000000000000000003fd"
  },
  "systemContracts": {
    "ADDRESS_BOOK": "0x0000000000000000000000000000000000000400",
    "MAINNET_CREDIT": "0x0000000000000000000000000000000000000000",
    "REGISTRY": "0x0000000000000000000000000000000000000401"
  }
}
```

#### Sample JSON-RPC

##### With Future Fork Scheduled

The following RPC command, issued on Kairos when Prague was scheduled but not activated:

```bash
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_config","id":1}' http://localhost:8545
```

would return (after formatting):

```JSON
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "current": {
      "blobSchedule": null,
      "chainId": "0x3E9",
      "forkId": "0xbef71d30",
      "precompiles": {
        "BLAKE2F": "0x0000000000000000000000000000000000000009",
        "BN254_ADD": "0x0000000000000000000000000000000000000006",
        "BN254_MUL": "0x0000000000000000000000000000000000000007",
        "BN254_PAIRING": "0x0000000000000000000000000000000000000008",
        "ECREC": "0x0000000000000000000000000000000000000001",
        "FEE_PAYER": "0x00000000000000000000000000000000000003fe",
        "ID": "0x0000000000000000000000000000000000000004",
        "KZG_POINT_EVALUATION": "0x000000000000000000000000000000000000000a",
        "MODEXP": "0x0000000000000000000000000000000000000005",
        "RIPEMD160": "0x0000000000000000000000000000000000000003",
        "SHA256": "0x0000000000000000000000000000000000000002",
        "VALIDATE_SENDER": "0x00000000000000000000000000000000000003ff",
        "VMLOG": "0x00000000000000000000000000000000000003fd"
      },
      "systemContracts": {
        "ADDRESS_BOOK": "0x0000000000000000000000000000000000000400",
        "MAINNET_CREDIT": "0x0000000000000000000000000000000000000000",
        "REGISTRY": "0x0000000000000000000000000000000000000401"
      }
    },
    "next": {
      "blobSchedule": null,
      "chainId": "0x3E9",
      "forkId": "0x0929e24e",
      "precompiles": {
        "BLAKE2F": "0x0000000000000000000000000000000000000009",
        "BLS12_G1ADD": "0x000000000000000000000000000000000000000b",
        "BLS12_G1MSM": "0x000000000000000000000000000000000000000c",
        "BLS12_G2ADD": "0x000000000000000000000000000000000000000d",
        "BLS12_G2MSM": "0x000000000000000000000000000000000000000e",
        "BLS12_MAP_FP2_TO_G2": "0x0000000000000000000000000000000000000011",
        "BLS12_MAP_FP_TO_G1": "0x0000000000000000000000000000000000000010",
        "BLS12_PAIRING_CHECK": "0x000000000000000000000000000000000000000f",
        "BN254_ADD": "0x0000000000000000000000000000000000000006",
        "BN254_MUL": "0x0000000000000000000000000000000000000007",
        "BN254_PAIRING": "0x0000000000000000000000000000000000000008",
        "ECREC": "0x0000000000000000000000000000000000000001",
        "FEE_PAYER": "0x00000000000000000000000000000000000003fe",
        "ID": "0x0000000000000000000000000000000000000004",
        "KZG_POINT_EVALUATION": "0x000000000000000000000000000000000000000a",
        "MODEXP": "0x0000000000000000000000000000000000000005",
        "RIPEMD160": "0x0000000000000000000000000000000000000003",
        "SHA256": "0x0000000000000000000000000000000000000002",
        "VALIDATE_SENDER": "0x00000000000000000000000000000000000003ff",
        "VMLOG": "0x00000000000000000000000000000000000003fd"
      },
      "systemContracts": {
        "ADDRESS_BOOK": "0x0000000000000000000000000000000000000400",
        "HISTORY_STORAGE_ADDRESS": "0x0000f90827f1c53a10cb7a02335b175320002935",
        "MAINNET_CREDIT": "0x0000000000000000000000000000000000000000",
        "REGISTRY": "0x0000000000000000000000000000000000000401"
      }
    },
    "last": {
      "blobSchedule": null,
      "chainId": "0x3E9",
      "forkId": "0x0929e24e",
      "precompiles": {
        "BLAKE2F": "0x0000000000000000000000000000000000000009",
        "BLS12_G1ADD": "0x000000000000000000000000000000000000000b",
        "BLS12_G1MSM": "0x000000000000000000000000000000000000000c",
        "BLS12_G2ADD": "0x000000000000000000000000000000000000000d",
        "BLS12_G2MSM": "0x000000000000000000000000000000000000000e",
        "BLS12_MAP_FP2_TO_G2": "0x0000000000000000000000000000000000000011",
        "BLS12_MAP_FP_TO_G1": "0x0000000000000000000000000000000000000010",
        "BLS12_PAIRING_CHECK": "0x000000000000000000000000000000000000000f",
        "BN254_ADD": "0x0000000000000000000000000000000000000006",
        "BN254_MUL": "0x0000000000000000000000000000000000000007",
        "BN254_PAIRING": "0x0000000000000000000000000000000000000008",
        "ECREC": "0x0000000000000000000000000000000000000001",
        "FEE_PAYER": "0x00000000000000000000000000000000000003fe",
        "ID": "0x0000000000000000000000000000000000000004",
        "KZG_POINT_EVALUATION": "0x000000000000000000000000000000000000000a",
        "MODEXP": "0x0000000000000000000000000000000000000005",
        "RIPEMD160": "0x0000000000000000000000000000000000000003",
        "SHA256": "0x0000000000000000000000000000000000000002",
        "VALIDATE_SENDER": "0x00000000000000000000000000000000000003ff",
        "VMLOG": "0x00000000000000000000000000000000000003fd"
      },
      "systemContracts": {
        "ADDRESS_BOOK": "0x0000000000000000000000000000000000000400",
        "HISTORY_STORAGE_ADDRESS": "0x0000f90827f1c53a10cb7a02335b175320002935",
        "MAINNET_CREDIT": "0x0000000000000000000000000000000000000000",
        "REGISTRY": "0x0000000000000000000000000000000000000401"
      }
    }
  }
}
```

##### Without Future Fork Scheduled

When no future forks are configured, the same RPC command would return:

```JSON
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "current": {
      "blobSchedule": null,
      "chainId": "0x3E9",
      "forkId": "0xbef71d30",
      "precompiles": {
        "BLAKE2F": "0x0000000000000000000000000000000000000009",
        "BN254_ADD": "0x0000000000000000000000000000000000000006",
        "BN254_MUL": "0x0000000000000000000000000000000000000007",
        "BN254_PAIRING": "0x0000000000000000000000000000000000000008",
        "ECREC": "0x0000000000000000000000000000000000000001",
        "FEE_PAYER": "0x00000000000000000000000000000000000003fe",
        "ID": "0x0000000000000000000000000000000000000004",
        "KZG_POINT_EVALUATION": "0x000000000000000000000000000000000000000a",
        "MODEXP": "0x0000000000000000000000000000000000000005",
        "RIPEMD160": "0x0000000000000000000000000000000000000003",
        "SHA256": "0x0000000000000000000000000000000000000002",
        "VALIDATE_SENDER": "0x00000000000000000000000000000000000003ff",
        "VMLOG": "0x00000000000000000000000000000000000003fd"
      },
      "systemContracts": {
        "ADDRESS_BOOK": "0x0000000000000000000000000000000000000400",
        "MAINNET_CREDIT": "0x0000000000000000000000000000000000000000",
        "REGISTRY": "0x0000000000000000000000000000000000000401"
      }
    },
    "next": null,
    "last": null
  }
}
```

## References

- [EIP-7910](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7910.md)
- [EIP-6122](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-6122.md)
- [EIP-4844](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-4844.md)

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
