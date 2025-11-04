Masked AES Implementation

This project implements a masked version of the AES-128 block cipher, designed to mitigate side-channel attacks such as Differential Power Analysis (DPA).
The implementation is written in Python for clarity and testing, following the principle of secure computation through secret sharing.

What Is Masked AES?

In the standard AES algorithm, all intermediate values (bytes, round keys, S-box outputs, etc.) are processed directly in memory.
This makes AES vulnerable to side-channel leakage, where an attacker can analyze physical characteristics such as power consumption or electromagnetic emissions to infer secret data.

Masked AES introduces randomness into the computation by splitting each sensitive value into two or more shares.
Each share alone reveals nothing about the true value.

For a byte x, masking is represented as:

x = x₀ ⊕ x₁


where

x₀ is a random mask

x₁ = x ⊕ x₀ is the complementary masked share

All AES operations are then performed on these masked shares instead of on the unprotected values.

Project Structure
Core Components
Function	Description
key_exp(key)	Expands a 128-bit AES key into the full round-key schedule.
mask_exp_keys(exp_key)	Applies random masking to each round key, producing masked shares for secure round-key usage.
masked_aes(text, exp_key, enc=1)	Performs masked AES encryption (enc=1) or decryption (enc=0) on a shared input state using the masked key schedule.
masked_sbox(byte_shares)	Implements the non-linear AES S-box transformation in a way that preserves masking correctness.
mix_columns_masked(state)	Executes the MixColumns transformation on masked shares without leaking intermediate values.
How It Works
1. State Representation

Each AES state byte is represented as a pair [share₀, share₁] such that:

real_byte = share₀ ⊕ share₁


This ensures that any single share is uniformly random and leaks no information about the true value.

2. Key Expansion

Round keys are first generated using key_exp().
Each round key is then masked independently with random values using mask_exp_keys(), ensuring that key material is never exposed during encryption.

3. Encryption Process

The masked_aes() function executes AES rounds using mask-preserving operations:

AddRoundKey: XORs masked state shares with masked round-key shares.

SubBytes: Uses a masked S-box that preserves the mask relationship during non-linear transformation.

ShiftRows / MixColumns: These linear transformations are applied share-wise, maintaining XOR linearity.

After all rounds, the two shares are recombined to reveal the final ciphertext:

cipher_byte = share₀ ⊕ share₁

Security Notes

This implementation uses first-order masking (two shares).

To protect against higher-order DPA, the design can be extended to d+1 shares.

The quality of randomness and correct mask synchronization are crucial for real-world side-channel resistance.

This project is intended for educational and research purposes only, not for production use.

Future Work

Add mask refreshing in the AES round logic to prevent mask reuse.

Extend masking to higher-order (d+1) schemes.

Develop additional test vectors and automated verification against NIST AES Known-Answer Tests (KATs).
