---
CIP: 1855
Title: Forging policy keys for HD Wallets
Authors: Samuel Leathers <samuel.leathers@iohk.io>, John Lotoski <john.lotoski@iohk.io>, Michael Bishop <michael.bishop@iohk.io>, David Arnold <david.arnold@iohk.io>
Comments-Summary: Multi-party transaction signing and key management for HD wallets.
Comments-URI: https://github.com/cardano-foundation/CIPs/wiki/Comments:CIP-1855
Status: Active
Type: Standards
Created: 2021-06-02
License: CC-BY-4.0
---

# Abstract

This document describes how to derive forging policy keys used for minting/burning tokens.

## Glossary


Term     | Definition
---      | ---
HD       | Hierarchical Deterministic, refers to wallets as described in [BIP-0032].

# Motivation

## Overview 

Forging tokens is derived from a script policy. The script policy includes hashes of keys needed to forge new tokens and must be witnessed by these keys in such a way as the script stipulates.
This CIP defines the derivation path at wich parties are expected to derive such keys.

# Specification

## HD Derivation

We consider the following HD derivation paths similarly to [CIP-1852]:

```
m / purpose' / coin_type' / policy_ix'
```


To associate policy keys to a wallet, we reserve however `purpose=1855'` for policy keys for forging tokens. The coin type remains `coin_type=1815'` to identify Ada as registered in [SLIP-0044]. We use a hardened index for each policy key as derivation is not needed.

We can summarize the various paths and their respective domain in the following table:

| `purpose` | `coin_type` | `policy_ix`         |
| ---       | ---         | ---                 |
| `1855'`   | `1815'`     | `[2^31 .. 2^32-1]` |

## CIP-0005 prefixes

To distinguish such keys & derived material in the human readable prefix of the bech32 representation, we introduce the following prefixes for insertion into CIP-0005:

#### Keys

| Prefix             | Meaning                                            | Contents                           |
| ---                | ---                                                | ---                                |
| `policy_sk`        | CIP-1855's policy private key                      | Ed25519 private key                |
| `policy_vk`        | CIP-1855's policy public key                       | Ed25519 public key                 |

#### Hashes

| Prefix             | Meaning                                            | Contents                                               |
| ---                | ---                                                | ---                                                    |
| `policy_vkh`       | CIP-1855's Policy verification key hash            | blake2b\_224 digest of a policy verification key       |

### Rationale
- ERC20 Converter IOHK is developing needs to keep track of policy keys. Rather than having randomly generated policy keys, a policy key can be associated with a mnemonic which is easier to backup.
- A 3rd party may want to have multiple tokens tied to same mnemonic, so we allow an index to specify the token.
- Contrary to CIP 1852, we don't use the `role` and `index` levels of the derivation path, since index is expressed at the 3rd level and no roles for policy signing keys are currently anticipated.

- No prefixes are defined for extended keys, since currently this CIP does not define further derivations.

- We use a different purpose for mainly two reasons:

  - It prevents mixing up standard wallets with policy keys used for forging.

  - Using a different purpose also fits well the use-case on hardware wallets who can still rely on a single root seed to manage many types of wallets. 

### Examples

- `m/1855’/1815’/0’`
- `m/1855’/1815’/1’`
- `m/1855’/1815’/2’`


# Backwards Compatibility

N/A (no preceding implementation or design).

# Reference Implementation

None yet.

# Related Work

Description                                  | Link
---                                          | ---
BIP-0032 - HD Wallets                        | https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki
CIP-5 - Common Bech32 Prefixes               | https://github.com/cardano-foundation/CIPs/tree/master/CIP-0005
CIP-1852 - Cardano HD Wallets                | https://github.com/cardano-foundation/CIPs/tree/master/CIP-1852
A Formal Specification of the Cardano Ledger | https://hydra.iohk.io/job/Cardano/cardano-ledger-specs/shelleyLedgerSpec/latest/download-by-type/doc-pdf/ledger-spec


# Copyright

CC-BY-4.0

[BIP-0032]: https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki
[CIP-0005]: https://github.com/cardano-foundation/CIPs/tree/master/CIP-0005
[CIP-1852]: https://github.com/cardano-foundation/CIPs/blob/master/CIP-1852
[ledger-spec.pdf]: https://hydra.iohk.io/job/Cardano/cardano-ledger-specs/shelleyLedgerSpec/latest/download-by-type/doc-pdf/ledger-spec
[SLIP-0044]: https://github.com/satoshilabs/slips/blob/master/slip-0044.md
