# Bech32 and Bech32m Overview

Bech32m is a version of Bech32 with a small change in a constant value.  
Bech32 and Bech32m addresses have the same characters, except for the last six characters, which are the checksum.  
A wallet needs to know whether it’s working with Bech32 or Bech32m to validate the checksum correctly.  
Both formats include an internal version byte to help the wallet easily determine which version is in use.

## Why Bech32m?

Bech32m is currently the recommended format for Bitcoin wallets because it can handle both Bech32 and Bech32m addresses.  
We'll focus on how Bech32m addresses are encoded and parsed, since they cover both formats.

## Components of a Bech32m Address

A Bech32m address consists of two main parts:

1. **Human Readable Part (HRP)**: This tells you what network the address belongs to.
   - For Bitcoin mainnet, the HRP is `"bc"`.
   - For Bitcoin testnet, the HRP is `"tb"`.
   
2. **Separator**: The separator is the character `"1"`.
   - It was chosen because using a colon (`:`) caused issues with certain programs that don’t handle it well.
   - The number `"1"` is safe to use and avoids confusion with the lowercase letter `"l"`.

## Data Part of a Bech32m Address

The data part consists of three components:

1. **Witness Version**:
   - This is a single byte (one character) immediately after the separator.
   - It represents the segwit version.
     - `"q"` represents version 0 (Segwit v0), and `"p"` represents version 1 (Segwit v1, or Taproot).
     - There are 17 possible witness versions for Segwit.

2. **Witness Program**:
   - This is a byte array (2 to 40 bytes) after the witness version.
   - For Segwit v0, the length must be 20 or 32 bytes.
   - For Segwit v1, it’s currently 32 bytes, though this may change in the future.

3. **Checksum**:
   - The checksum is 6 characters long and helps detect errors.
   - It uses a BCH (error detection) code, but for Bitcoin addresses, it’s only used for error detection, not correction.

## Bitcoin Address Examples

Here are some types of Segwit outputs and how they’re structured:

1. **P2WPKH (Pay to Witness Public Key Hash)**:
   - Contains a commitment (a hash of a public key) as the witness program.

2. **P2WSH (Pay to Witness Script Hash)**:
   - Uses a script hash (SHA256) as the witness program.

3. **P2TR (Pay to Taproot)**:
   - The witness program is a public key on the secp256k1 curve (which may be a commitment to additional data).

For a future Segwit version (not yet defined), we use a version number of 16 and the smallest allowed witness program (2 bytes).

## Creating Addresses

The process of generating these addresses is based on specific scripts:

- For example, **P2WPKH** uses a public key (SHA256 → RIPEMD-160), and **P2WSH** uses a SHA256 of the script.
- **P2TR** addresses involve using public key data on the secp256k1 curve, possibly with extra data.
