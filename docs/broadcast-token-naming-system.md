        Title: Broadcast Token Naming System (BTNS)
        Author: Jeremy Johnson (J-Dog) & Stamp Devs
        Status: Draft
        Type: Standards
        Created: 2023-05-12

# Abstract
Establish new token naming system via Broadcasts

# Motivation
Allow experimentation with an alternate token naming system

# Rationale
Counterparty has established a beautiful asset naming system which allows for registration of unique assets and subassets, but it requires the use of XCP as an anti-spam mechanism. One of the criticizms of Counterparty has been that it requires XCP in order to use the platform. This is untrue, as you can use Counterparty and issue a numeric asset using only BTC. However, it is clear that some people see this "anti-spam" mechanism as unnecessary, and there is a desire for possible alternate naming systems to be explored.

This proposal establishes a new token naming system which will allow additional experimentation with "tokens" on Bitcoin via the Counterparty `broadcast` system.

By establishing 3 pre-defined broadcast formats, users can `DEPLOY`, `MINT`, and `TRANSFER` tokens. With these 3 functions we can create tokens, allow users to mint them in a decentralized "fair" way, and allow for the moving of these new tokens between addresses. 

This spec can be extended in the future to allow for additional options and formats.

This spec was inspired in part by brc-20 and src-20 and seeing the desire to experiment with new token naming systems.

# Definitions

- `token` - A virtual token which was created via a `MINT` format `broadcast` transaction
- `broadcast` - A general purpose transaction type which allows broadcasting of a message to the Counterparty platform
- `DEPLOY` - A specially formatted `broadcast` which registers a `token` name for usage
- `MINT` - A specially formatted `broadcast` which allows for creation/minting of token supply 
- `TRANSFER` - A specially formatted `broadcast` which can move a `token` between users

# Specification
This spec defines 3 formats `DEPLOY`, `MINT`, and `TRANSFER` which will allow for creation of tokens, supply, and sending between users

## Project Prefix
In order for different projects to experiment with features in the Broadcast Token Naming System (BTNS) and not collide with one another, each project should establish a unique prefix for their project to use in their broadcasts, like `STAMP:`.

**Example 1**
`STAMP:DEPLOY|JDOG|1000|1|0`

**Example 2**
`STAMP:MINT|JDOG|1`


## `DEPLOY` format
This format allows one to create a token and specify the following information about it

- `NAME` - 1 to 250 characters in length (a-zA-Z0-9.-_@! - rules below)
- `MAX_SUPPLY` - Maximum token supply (max: 18,446,744,073,709,551,615 - commas not allowed)
- `MAX_MINT` - Maximum amount of supply a `MINT` transaction can issue
- `DECIMALS` - Number of decimal places token should have (max: 18, default: 0)
- `MINT_SUPPLY` - Amount of token supply to mint in immediately (default:0)
- `TRANSFER` - Address to transfer ownership of the `token` to (owner can perform future actions on token)
- `TRANSFER_SUPPLY` - Address to transfer `MINT_SUPPLY` to (mint initial supply and transfer to address)

**Broadcast Format:**
`DEPLOY|NAME|MAX_SUPPLY|MAX_MINT|DECIMALS|MINT_SUPPLY|TRANSFER|TRANSFER_SUPPLY`

**Example 1:**
`DEPLOY|JDOG|1000|1|0`
The above example issues a JDOG token with a max supply of 1000, and a maximum mint of 1 JDOG per `MINT`

**Example 2:**
`DEPLOY|BRRR|1000000|100|0|100`
The above example issues a BRRR token with a max supply of 1,000,000, and a maximum mint of 100 BRRR per `MINT`

**Example 3:**
`DEPLOY|TEST|100|1|0|1|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev`
The above example issues a TEST token with a max supply of 100, and a maximum mint of 1 TEST per `MINT`. This also mints 1 TEST token, and transfers ownership AND initial token supply to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev

## `MINT` format
This format allows one to mint token supply

- `NAME` - `token` name registered with `DEPLOY` format
- `AMOUNT` - Amount of tokens to mint
- `TRANSFER` - Address to transfer tokens to

**Broadcast Format:**
`MINT|NAME|AMOUNT|TRANSFER`

**Example 1:**
`MINT|JDOG|1`
The above example mints 1 JDOG `token` to the broadcasting address

**Example 2:**
`MINT|BRRR|100|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev`
The above example mints 100 BRRR tokens and transfers them to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev 

## `TRANSFER` format
This format allows one to transfer or send a `token` between addresses

- `NAME` - `token` name registered with `DEPLOY` format
- `AMOUNT` - Amount of tokens to send
- `DESTINATION` - Address to transfer tokens to

**Broadcast Format:**
`TRANSFER|NAME|AMOUNT|DESTINATION`

**Example 1:**
`TRANSFER|JDOG|1|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev`
The above example sends 1 JDOG token to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev

**Example 2:**
`TRANSFER|BRRR|5|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev`
The above example sends 5 BRRR tokens to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev

## Open Questions
- Should we allow for special symbols like in src-20? (will require base64 encoding name)
- Should we put in basic rule framework (min/max supply, first issue on name is `valid`, mint until `MAX_SUPPLY`, etc)

# Copyright
This document is placed in the public domain.