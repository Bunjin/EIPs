---
eip: TBD
title: Hierarchical Deterministic path for personas, cross-protocol wallet accounts.
author: Vincent Eli (@Bunjin), Dan Finlay (@DanFinlay)
discussions-to: TBD
status: Draft
type: Standards Track
category: ERC
created: 2019-12-02
---

<!--You can leave these HTML comments in your merged EIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new EIPs. Note that an EIP number will be assigned by an editor. When opening a pull request to submit your EIP, please use an abbreviated title in the filename, `eip-draft_title_abbrev.md`. The title should be 44 characters or less.-->
## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the EIP.-->

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
In this EIP we define the concept of personas, a new type of account, and propose a standardized HD path `m / 101' / persona_index` to derive these accounts in a manner that can allow for cross wallet compatibility.

## Motivation
<!--The motivation is critical for EIPs that want to change the Ethereum protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the EIP solves. EIP submissions without sufficient motivation may be rejected outright.-->
EIP 1775 introduced the concept of personas: cryptographic keys (or accounts) that can be used as identities used to interact with an application (and generate its application key based on its origin) and aimed at being separated (non correlatable) from one to another. Here we further define the concept of personas and propose a standard to derive them.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Ethereum platforms (go-ethereum, parity, cpp-ethereum, ethereumj, ethereumjs, and [others](https://github.com/ethereum/wiki/wiki/Clients)).-->

Personas can be thought of as identities, profiles, or masks an user can use to interact with applications and attempting to not being traced across these identities by the application itself or any other observer. A persona is it itself a cryptographic key (or account). The persona key can be used to perform cryptographic operations directly, or used to generate new keys (such as EIP1775 app keys) to delegate operations to external agents. Therefore, by extension, since it refers to an user identity, it also designates the set of accounts or keys associated (correlatable) to this identity and may operate on many protocols.

### Personas as fundamental identity accounts

These fundamental accounts, personas, are cryptocurrency and protocol agnostic (protocols include non currency or blockchain related cryptography such as chats, databases or anything requiring user-side 256 bit cryptography). Indeed these personas could exist outside of any protocol, and more importantly, they are not subject to any given protocol, but rather theoretically pre-exist them.
They could also be used outside of EIP1775 as fundamental keys used to perform fundamental identity related cryptographic operations.

A persona key could be any 256 bit key. EIP1775 requires a private keys to generate its application keys, and can be generated from any private key. If personas are used directly to perform standardized signing operations, a specification for signing and generating public keys will have to be provided, but it does not need to be tied to a unique protocol. Thus, persona keys are not restricted to any cryptographic specification (such as Bitcoin or Ethereum secp256k1 Elliptic Curve Digital Signing Algorithm - ECDSA) for the generation of public keys, addresses and the signing operations.

### Using Hierarchical Deterministic specification in an cryptocurrency agnostic manner

We propose to use the BIP32 Hierarchical Deterministic specification since it is a commonly used clean framework with interesting properties for generating (extended or non extended) private / public key pairs. Even if this specification originates from the secp256k1 ECDSA world of Bitcoin, it can be used in a cryptographically agnostic fashion.

However we don't want to use the same path as any existing protocol keys, such as SLIP44 paths, because using a given protocol's accounts (such as Ethereum keys) as personas roots would be incompatible with the protocol-agnostic nature of the persona keys and could lead to confusion and dangers, such as the inability to transfer an Ethereum key without transferring all of that persona's other keys, but not vice versa.

Therefore for persona keys, we use a new BIP43 purpose field in a way that can not collide with the existing BIP process of attributing new BIP43 purpose fields for the Bitcoin community. However, since BIP43 is cryptocurrency agnostic, the attribution of BIP43 purpose fields should be set outside of the BIP process (In the past several projects such as Ledger Wallet SSH and PGP, apps have used BIP43 purpose fields outside of the BIP process).
More importantly, this proposal, being cryptocurrency or even protocol agnostic (because it englobes them all), we believe that it shouldn't be tied to any cryptocurrency (neither Bitcoin, nor Ethereum, even if this is initially proposed as an EIP). In the same way BIP44s span SLIP44 (non BIP process subjected list of BIP44 cryptocurrencies), BIP43 should generate a protocol agnostic repository for listing BIP43 purpose fields (we provide a static list at time of writing in this document).

### HD path for Personas

The BIP 32 and 43 specifications invite us to use a path

`m / purpose' / *`

For `purpose` we need to use a number that is not going to collide with any other purpose.


Here are the BIP43 purpose fields used at time of writing:

| code | Reference                                                                | Title |
|------|--------------------------------------------------------------------------|-------|
| 44 | [BIP44](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki) | Multi-Account Hierarchy for Deterministic Wallets      |
| 45 | [BIP45](https://github.com/bitcoin/bips/blob/master/bip-0045.mediawiki) | Structure for Deterministic P2SH Multisignature Wallets|
| 48 | [SLIP48](https://github.com/satoshilabs/slips/issues/49) | Deterministic Key Hierarchy for Graphene-based Networks |
| 49 | [BIP49](https://github.com/bitcoin/bips/blob/master/bip-0049.mediawiki) |  Derivation scheme for P2WPKH-nested-in-P2SH based accounts |
| 80 | [BIP80](https://github.com/bitcoin/bips/blob/master/bip-0080.mediawiki) | Hierarchy for Non-Colored Voting Pool Deterministic Multisig Wallets |
| 84 | [BIP84](https://github.com/bitcoin/bips/blob/master/bip-0084.mediawiki) | Derivation scheme for P2WPKH based accounts |
| 535348 | [Ledger app ssh](https://github.com/LedgerHQ/ledger-app-ssh-agent/blob/master/getPublicKey.py#L49) | |
| 80475047| [PGP/SSH Ledger](https://github.com/LedgerHQ/ledger-app-openpgp-card/blob/master/doc/developper/gpgcard3.0-addon.rst#deterministic-key-derivation)| |
| 101| Personas| Fundamental Identity Accounts|


We also listed above the code `101` that we propose to use for these fundamental identity accounts, personas. It is a number BIP that was withdrawn (later refined and proposed again as BIP109 and it was rejected) and the topic of this BIP is not related to cryptographic keys so there is no chance that it can some day span a purpose field.

`m / 101' / [rest of path TBD]`

We will start by enumerating personas as depth 2 childs:
`m / 101' / persona_index`

Thus `m / 101' / 0`for the first persona, `m / 101' / 1`for the second, etc.

Leaving the specification open towards the `rest of the path` allows potentially for creative use of personas, if some are uncovered.  

### Personas are not secp256k1 restricted

Regarding the ability to use the persona keys directly (instead of using application keys generated from these persona keys), our proposal does not wish to restrict persona keys only to seck1p ECDSA.
Note that the BIP32 specification (since it was originally designed for cryptocurrency) de facto implies secp1k ECDSA cryptography for the public keys derivation part. However, since we mostly care about private keys for personas (for now), persona keys defined in the manner defined in this proposal will not necessarly be restricted to use secp1k ECDSA cryptography in the future (it would allow for any 256 bits private keys digital signature algorithm, such as non secp1k ones such as Zk-starks among others).
While generating child private keys from the parent private key in the same way as BIP32, the public keys can be generated in alternative ways, for instance using other elliptic curve parameters. The alternate ways to secp1k ECDSA will not necessarily preserve the ability to compute public child keys from a non hardened parent public extended key. However, we can still derive their private keys using HD trees and benefit from the englobing cleaness and standardization of this framework.

### Personas are intented to be not correlatable

Personas are designed to be kept separate, and to offer the possibility of privacy between personas. Note that this is an intention, yet true unability to correlate personas would depend on usage of each of the protocols. Some protocol operations are privacy preserving, yet today sending funds on most cryptocurrencies isn't.

Indeed the funding trail is something that can to different extents correlate personas together and reduce the benefit of using distinct personas. If, for instance, one uses the same Ethereum account (or different but correlatable accounts) to fund two distinct persona generated accounts, then these two personas would not preserve non-correlated privacy. With analytical tools, an application could be able to relate these two personas and know that there is a single user behind them.

We (both as proposers and community members) should proceed to develop wallet tools that allow a user to more privately fund distinct personas in a non correlatable way, and even allow for private methods of funding personas. These tools could include privacy preserving ways of transferring cryptocurrency (eg. Mixers, Zk-snarks...), and analytics to verify at any given time that personas are not correlatable (this should be based on a proper measure of likelihood of correlation).

Another source of potential correlation that, as wallet developers, we should try to assist users to avoid is user tracking across personas through IP address or other browser tracking techniques.

### Personas and protocol accounts, measuring correlation: [WIP]

Once protocol accounts have been used to interact (eg. funding) with a persona account (directly with the persona key, or with its application keys, or with any protocol account already linked to that persona), they become correlated to that persona if the method used was not privacy preserving. It will also link to this persona any other protocol account that were already correlated to the one just used.
Designing good privacy preserving practices and assisting the user to follow them is not too hard at the wallet level.

However two more complex topics should be researched:
a) Identifying pre-existing correlations:
We need to develop a measure and analytical tools (such as clustering algorithms) that can determine whether two accounts are correlated by transactions and belong to the same user.
For that we need to survey not only the blockchain protocol operations such as ether transfer but also the transactions on associated protocols such as ERC20 transactions that can privacy threatening too.
Note that the measure of correlation should be a non binary one. 
b) Allowing to store and export the list of persona / protocol accounts correlation


### Example derivation of persona accounts:

24 word seed:

`decrease major dwarf chase wear duck trust vital three slot clump clip voyage bid kind achieve cruel garden guitar magnet crater destroy strategy extend`

Agnostic Personas(private keys in base 16):

| path     | private key                                                        |
|----------|--------------------------------------------------------------------|
| m/101'/0 | 0x2f88edf4b959dbe6cc8be9ce2f25db8bd3706fa80342442853c8eb1efc76543f |
| m/101'/1 | 0xd606accfd1c46b7f6a20407533663b2e3585e927a56c51e804233e0d28125d0b |
| m/101'/2 | 0x4aaf427f33c2c19471003f34acf83c48d5fdab355b967a5b2a8c0e3ab428ffe6 |

As Bitcoin accounts (private keys in base 52):

| path     | address                            | public key                                                           | private key                                          |
|----------|------------------------------------|----------------------------------------------------------------------|------------------------------------------------------|
| m/101'/0 | 1FTiNGTKUWRvbu4MFhBHDLuaWsmuCA4q3o | 0x03e47370f983164e2817bef9beb932c3574098d529aeae8b2b03efc60c0f3301d9 | Kxp7UdW6Jn4A4cDuCxWS2x2xh2rmdwtnKrKQhYwukvxvjNQk1zVq |
| m/101'/1 | 19sRPE9YTyjoQQ4hedgfL3hb4UPc5MTHAa | 0x02d8a8833b7565d089fc3df88a06ddc9303495bc92f601ffc81c2d6e41dd7d258c | L4PkRv3KxeSfGrYobLNQqjit4NapuoVikyAywLBBHEhWJzVe8Lwo |
| m/101'/2 | 1CMBTh1o712E9fmXtN5Fb5xKJ3EvBtGqa1 | 0x038841447982c5ade980056fb83cabed9d57ee98fd25552c9eafe5bb9fe1ad0e93 | KyitTVF4PYpep14uoLUZ2HLarDdXCun12ZzgMtYpLDbXomJnLtB1 |

As Ethereum accounts (private keys in base 16):

| path     | address                                    | public key                                                           | private key                                                        |
|----------|--------------------------------------------|----------------------------------------------------------------------|--------------------------------------------------------------------|
| m/101'/0 | 0x0AA2cBbC8b7FE4d209bf67a3B29aBB79AC69Bdb7 | 0x03e47370f983164e2817bef9beb932c3574098d529aeae8b2b03efc60c0f3301d9 | 0x2f88edf4b959dbe6cc8be9ce2f25db8bd3706fa80342442853c8eb1efc76543f |
| m/101'/1 | 0x284DAce36273D794c70727f63a04c62687513A85 | 0x02d8a8833b7565d089fc3df88a06ddc9303495bc92f601ffc81c2d6e41dd7d258c | 0xd606accfd1c46b7f6a20407533663b2e3585e927a56c51e804233e0d28125d0b |
| m/101'/2 | 0x6938Fe11F61C438f2e1c822C64833201c3c4A106 | 0x038841447982c5ade980056fb83cabed9d57ee98fd25552c9eafe5bb9fe1ad0e93 | 0x4aaf427f33c2c19471003f34acf83c48d5fdab355b967a5b2a8c0e3ab428ffe6 |

## Backwards Compatibility
<!--All EIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The EIP must explain how the author proposes to deal with these incompatibilities. EIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
We made sure that the purpose field is not used already and can not be used in the future through the BIP process. For forward compatibility, we must make sure that this purpose field is publicized enough such that another team (outside of the BIP process) does not propose to use it for another purpose. Forward compatibility with the existing BIP process is guaranteed since their process can not attribute the purpose field used.

## Implementation
<!--The implementations must be completed before any EIP is given status "Final", but it need not be completed before the EIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->

Coming soon.

## Alternative approaches:

a) BIP 43 (not BIP44 compliant but following the deviation standard)
m / purpose' / coin_type' / subpurpose' / [rest of path TBD]
m/43/60/EIP number/ ...
See here for the non merged standard proposed by a Bitcoin developer:
https://github.com/bitcoin/bips/pull/523

b) BIP43 + new pupose code
m / 101' / [rest of path TBD]

See discussion here with Nick Johnson, where he suggests option a) instead of b) that we propose in the current EIP.
https://ethereum-magicians.org/t/eip-erc-app-keys-application-specific-wallet-accounts/2742/27?u=bunjin
and here
https://ethereum-magicians.org/t/eip-erc-app-keys-application-specific-wallet-accounts/2742/31?u=bunjin


## Sources

https://bitcoin.stackexchange.com/questions/60470/is-there-a-comprehensive-list-of-registered-bip43-purposes
http://www.secg.org/sec2-v2.pdf
https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
