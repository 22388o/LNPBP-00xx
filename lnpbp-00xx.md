# LNPBP-00xx
```
LNPBP: 000xx
Vertical: Smart contracts
Title: Universal Loans for RGB Network 
Author: Rsync (22388o) <arealayer@gmail.com>
Status: Proposal
Type: Informational
Created: 2023-01-30
License: MIT license
```

- [Abstract](#abstract)
- [Background](#background)
- [Motivation](#motivation)
- [Specification](#specification)
  - [Bitwise structure of the identifier](#bitwise-structure-of-the-identifier)
  - [Distinguishing different types of identifier](#distinguishing-different-types-of-identifier)
- [Compatibility](#compatibility)
- [Rationale](#rationale)
  - [Estimating ranges for bit-based values](#estimating-ranges-for-bit-based-values)
- [Reference implementation](#reference-implementation)
- [Acknowledgements](#acknowledgements)
- [References](#references)
- [Copyright](#copyright)
- [Test vectors](#test-vectors)


## Abstract



## Background



## Specification

### Bitwise structure of the identifier



```
_ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _
^ ^                                                  ^   ^              ^   ^                                 ^   ^ ^                               ^
| |                                                  |   |              |   |                                 |   | |                               |
| +--------------------------------------------------+   +--------------+   +---------------------------------+   | +-------------------------------+
|                 Block height, 23 bits                 BlockHash checksum   Transaction position in the block    |     TxIn/TxOut index, 15 bits
Off-chain flag, set to 0                                      8 bits                     16 bits                  TxIn/TxOut flag
```

| Bits* | No bits | Possible no of values | Meaning |
|------:|--------:|----------------------:|---------|
|     0 |       1 | 2         | Flag indicating the structure for the rest of bits; MUST be set to 0 for the provided case |
|  1-23 |      23 | 8'388'608 | Block height; sufficient to cover >160 years of blockchain history |
| 24-31 |       8 | 256       | Checksum for block hash value: binary XOR of it's 8 bytes |
| 32-47 |      16 | 65'536    | Transaction position in the block; with 1 MB block size limit sufficient to cover all transactions even if their average size is 15 only |
|    48 |       1 | 2         | Flag indicating whether the next 15 bits represents transaction input (0) or output (1) index |
| 49-63 |      15 | 32'768    | Transaction input or output index (starting from 1) |

* In "most significant bit goes first" order


If the most significant bit of the short identifier is set to 1, the bits MUST
have the following meaning:

```
_ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _   _ _ _ _  _ _ _ _
^ ^                                                                                                           ^   ^ ^                               ^
| |                                                                                                           |   | |                               |
| +-----------------------------------------------------------------------------------------------------------+   | +-------------------------------+
|                                          Highest 47 bits from transaction id                                    |     TxIn/TxOut index, 15 bits
Off-chain flag, set to 1                                                                                          TxIn/TxOut flag
```

|Bits* | No bits | Possible no of values | Meaning  |
|------:|--------:|----------------------:|---------|
|    0 |       1 | 2         | Flag indicating the structure for the rest of bits; MUST be set to 1 for the provided case |
| 1-47 |      47 | >140 trillions (1.4*10^14) | Highest 47 bits from transaction id |
|   48 |       1 | 2         | Flag indicating whether the next 15 bits represents transaction input (0) or output (1) index |
|49-63 |      15 | 32'768    | Transaction input or output index (starting from 1) |

* Using big endian (network) byte order

### Distinguishing different types of identifier

The identifier may be used to denote 7 different types of Bitcoin blockchain-
related entities:

Entity             | Kind      | Distinguishing factor
-------------------|-----------|-----------------------
Block              | On-chain  | First bit set to 0, bits 32-63 set to 0
Transaction        | On-chain  | First bit set to 0, bits 48-63 set to 0
Transaction input  | On-chain  | First bit set to 0, bit 48 set to 0, at least one of the bits in range of 49-63 is a non-zero
Transaction output | On-chain  | First bit set to 0, bit 48 set to 1, at least one of the bits in range of 49-63 is a non-zero
Transaction        | Off-chain | First bit set to 1, bits 48-63 set to 0
Transaction input  | Off-chain | First bit set to 1, bit 48 set to 0, at least one of the bits in range of 49-63 is a non-zero
Transaction output | Off-chain | First bit set to 1, bit 48 set to 1, at least one of the bits in range of 49-63 is a non-zero


It should be noted, that coinbase transactions has the same identifier as the
block itself (since transaction indexes within the block are 0-based, unlike
transaction input and output indexes, which are 1-based): in this regard
coinbase transaction can be seen as a natural part of the block data.
Nevertheless, identifiers for coinbase transaction outputs and inputs are
distinguished from the unique short block identifier.


## Compatibility




## Rationale

### Estimating ranges for bit-based values



## Reference implementation

<https://github.com/LNP-BP/rust-lnpbp/pull/14>


## Acknowledgements



## References

1. <https://github.com/bitcoin/bitcoin>
2. Eric Lombrozo, Johnson Lau, Pieter Wuille. BIP-141: Segregated Witness
   (Consensus layer).
   <https://github.com/bitcoin/bips/blob/master/bip-0141.mediawiki#Transaction_ID>
3. BOLT #7: P2P Node and Channel Discovery.
   <https://github.com/lightningnetwork/lightning-rfc/blob/master/07-routing-gossip.md#definition-of-short_channel_id>
4. Pieter Wuille, Jonas Nick, Anthony Towns. BIP-341 Taproot: SegWit version 1
   output spending rules.
   <https://github.com/bitcoin/bips/blob/master/bip-0341.mediawiki>
5. Pieter Wuille, Jonas Nick, Tim Ruffing. BIP-340: Schnorr Signatures for
   secp256k1.
   <https://github.com/bitcoin/bips/blob/master/bip-0340.mediawiki>


## Copyright

This document is licensed under the MIT l license.


## Test vectors

TBD
