        Title: SRC-20.1
        Author: Jeremy Johnson (J-Dog) & Stamp Devs
        Status: Draft
        Type: Standards
        Created: 2023-05-???

# Abstract
Extend `SRC-20` to add more functionality.

# Motivation
Extend `SRC-20` functionality, allow for more efficient data encoding, and move entire system to Counterparty broadcasts.

# Rationale
[`BRC-20`](https://domo-2.gitbook.io/brc-20-experiment/) and [`SRC-20`](./src20.md) established new `token` naming systems, but have done so very inefficiently. The usage of `JSON` and `base64` encoding leads to bigger transaction sizes than necessary, making transactions more costly to mine.

The `Broadcast Token Naming System (BTNS)` specification establishes a token naming system via the `Counterparty` `broadcast` function. The `BTNS` is more efficient at storing data, because it uses a pre-defined format for its fields, doing away with the need for `JSON` and `base64` encoding which bloat transaction size.

`SRC-20.1` merges `SRC-20` and `BTNS` functionality to establish a new `STAMP` token name system. `SRC-20.1` tokens are deployed, minted, and transferred by broadcasting specially formatted messages using the `Counterparty` `broadcast` transaction type.

# Definitions
- `BRC-20` - Bitcoin Request For Comment 20
- `SRC-20` - Stamps Request For Comment 20
- `JSON` - Encoding format for representing Javascript object literals, arrays, and scalar data.
- `base64` - Encoding format designed to avoid issues with transfer of binary information
- `Counterparty` - A token platform on the Bitcoin blockchain
- `broadcast` - A special message type in the `Counterparty` platform
- `BTNS` - Broadcast Token Name System Specification


# Specification

## Project Prefix
`SRC-20.1` will use the `STAMP` and `ST` project prefixes. All `SRC-20.1` stamps will begin with `stamp:` or `st:` (case insensitive)

## DEPLOY
A `token` may be issued by performing a `Counterparty` `broadcast` using the `DEPLOY` command. The params that can be defined when issuing a `token` are :

- `TICK` - 1 to 250 characters in length (a-zA-Z0-9.-_@!)
- `MAX_SUPPLY` - Maximum token supply (max: 18,446,744,073,709,551,615 - commas not allowed)
- `MAX_MINT` - Maximum amount of supply a `MINT` transaction can issue
- `DECIMALS` - Number of decimal places token should have (max: 18, default: 0)
- `MINT_SUPPLY` - Amount of token supply to mint in immediately (default:0)
- `TRANSFER` - Address to transfer ownership of the `token` to (owner can perform future actions on token)
- `TRANSFER_SUPPLY` - Address to transfer `MINT_SUPPLY` to (mint initial supply and transfer to address)

**Broadcast Format:**
`st:DEPLOY|TICK|MAX_SUPPLY|MAX_MINT|DECIMALS|MINT_SUPPLY|TRANSFER|TRANSFER_SUPPLY`

**Example 1:**
`st:DEPLOY|STMP|1000|1|0`
The above example issues a STMP token with a max supply of 1000, and a maximum mint of 1 STMP per `MINT`

**Example 2:**
`st:DEPLOY|KEVIN|1000000|100|0|100`
The above example issues a KEVIN token with a max supply of 1,000,000, and a maximum mint of 100 KEVIN per `MINT`

**Example 3:**
`st:DEPLOY|TEST|100|1|0|1|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev`
The above example issues a TEST token with a max supply of 100, and a maximum mint of 1 TEST per `MINT`. This also mints 1 TEST token, and transfers ownership AND initial token supply to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev

## MINT
A `token` may be minted by performing a `Counterparty` `broadcast` using the `MINT` command. The params that can be defined when minting a `token` are :

- `TICK` - `token` name registered with `DEPLOY` format
- `AMOUNT` - Amount of tokens to mint
- `TRANSFER` - Address to transfer tokens to

**Broadcast Format:**
`st:MINT|TICK|AMOUNT|TRANSFER`

**Example 1:**
`st:MINT|STMP|1`
The above example mints 1 STMP `token` to the broadcasting address

**Example 2:**
`st:MINT|KEVIN|100|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev`
The above example mints 100 KEVIN tokens and transfers them to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev 

### Rules
- `token` supply may be minted until `MAX_SUPPLY` is reached.
- Transactions that attempt to mint supply beyond `MAX_SUPPLY` shall be considered invalid and ignored.

## TRANSFER
A `token` may be transferred by performing a `Counterparty` `broadcast` using the `TRANSFER` command. The params that can be defined when transferring a `token` are :

- `TICK` - `token` name registered with `DEPLOY` format
- `AMOUNT` - Amount of tokens to send
- `DESTINATION` - Address to transfer tokens to

This format also allows for repeating `AMOUNT` and `DESTINATION` to enable multiple transfers in a single transaction

**Broadcast Format:**
`st:TRANSFER|TICK|AMOUNT|DESTINATION`

**Broadcast Format2:**
`st:TRANSFER|TICK|AMOUNT|DESTINATION|AMOUNT|DESTINATION`

**Broadcast Format3:**
`st:TRANSFER|TICK|AMOUNT|DESTINATION|TICK|AMOUNT|DESTINATION`

**Example 1:**
`st:TRANSFER|STMP|1|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev`
The above example sends 1 STMP token to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev

**Example 2:**
`st:TRANSFER|KEVIN|5|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev`
The above example sends 5 KEVIN tokens to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev

**Example 3:**
`st:TRANSFER|STMP|5|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev|1|1BoogrfDADPLQpq8LMASmWQUVYDp4t2hF9`
The above example sends 5 STMP tokens to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev and 1 STMP token to 1BoogrfDADPLQpq8LMASmWQUVYDp4t2hF9

**Example 4:**
`st:TRANSFER|STMP|5|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev|KEVIN|3|1BoogrfDADPLQpq8LMASmWQUVYDp4t2hF9`
The above example sends 5 STMP tokens to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev and 3 KEVIN token to 1BoogrfDADPLQpq8LMASmWQUVYDp4t2hF9

### Rules
- A `token` transfer shall only be considered valid if the broacasting address has balances of the `token` to cover the transfer `AMOUNT`
- A `token` transfer that does _not_ have `AMOUNT` in the broadcasting address shall be considered invalid and ignored.
- A valid `token` transfer will deduct the `token` `AMOUNT` from the broadcasting addresses balances
- A valid `token` tranfer will credit the `token` `AMOUNT` to the `DESTINATION` address

## Summary

`SRC-20.1` extends `SRC-20` with the following changes :
1. Extends `TICK` to allow for up to 250 characters
2. Disallows usage of special characters `|` (pipe) and `;` (semi-colon)
3. Requires `TICK` to be `base64` encoded if it includes any unicode characters
4. Establishes that `SRC-20.1` system will live `Counterparty` `broadcast` system
5. Allows for `tokens` to be minted during `DEPLOY`
6. Allows for transfer of `token` ownership to an address during `DEPLOY`
7. Allows for transfer of `token` supply to an address during `DEPLOY`
8. Allows for transfering of multiple `tokens` to multiple addresses in the same transaction (multi-send)
9. Introduces the `STAMP` token as an anti-spam mechanism, and to help fund and sustain Stamps/SRC ecosystem.
10. Requires 1 `STAMP` token in broadcasting address when `DEPLOY` transaction is mined.


# Copyright
This document is placed in the public domain.


