Masked AES Implementation

This project implements a masked version of the AES-128 block cipher, designed to mitigate side-channel attacks (such as Differential Power Analysis, DPA). The implementation is written in Python for clarity and testing, following the principle of secure computation through secret sharing.

What Is Masked AES?

In standard AES, all intermediate values (bytes, round keys, S-box outputs, etc.) are processed directly in memory.
This makes it vulnerable to side-channel leakage — an attacker can correlate power consumption or electromagnetic emissions with internal state values to recover the secret key.

Masked AES introduces randomness into the computation by splitting each sensitive value into two or more shares.
Each share alone reveals nothing about the true value.

For example, for a byte x, we represent it as:

x = x₀ ⊕ x₁


where x₀ is a random mask and x₁ = x ⊕ x₀ is the masked share.
Every AES operation is then adapted to operate on these masked shares.

Project Structure
Core Components
Function	Description
key_exp(key)	Expands a 128-bit AES key into the full round key schedule.
mask_exp_keys(exp_key)	Applies random masking to each round key, producing masked shares for secure round-key usage.
masked_aes(text, exp_key, enc=1)	Performs masked AES encryption (enc=1) or decryption (enc=0) on a shared input state using the masked key schedule.
masked_sbox(byte_shares)	Non-linear substitution box implementation that preserves masking correctness.
mix_columns_masked(state)	Performs the MixColumns transformation on masked shares without leaking intermediate values.
🔧 How It Works
1. State Representation

Each AES state byte is represented as a pair [share₀, share₁] such that:

real_byte = share₀ ⊕ share₁


This ensures that any single share appears uniformly random and leaks no information individually.

2. Key Expansion

The expanded round keys are first computed normally using key_exp(), then each key word is masked independently with a random mask using mask_exp_keys().

3. Encryption Process

The masked_aes() function applies AES rounds using masked operations:

AddRoundKey: XORs masked state shares with masked key shares.

SubBytes: Uses a masked S-box, ensuring the non-linear transformation does not destroy the mask.

ShiftRows / MixColumns: Linear operations are performed share-wise (⊕-linear).

After all rounds, the two shares are recombined to reveal the final ciphertext.


Security Notes

This is a first-order masking implementation (two shares).

Higher-order masking (three or more shares) can be added for protection against higher-order DPA.

Randomness quality and synchronization between masks are crucial for real-world security.

This implementation is for educational and testing purposes, not for production use.

Future Work :

1) Add Refreshs on the right places in the core AES logic

2) Extend masking to d+1 shares

3) Make more tests
