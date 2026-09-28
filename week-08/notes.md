# Week 8 Notes — Practical Cryptography

**Student Name:** Dominique D Fleming

**Date:** September 27, 2026

## Vocabulary in My Own Words

```text
Plaintext: The original information that I can read and understand.
Ciphertext: Information that has been encrypted so I cannot read or understand it without the correct key or passphrase.
Encryption: Protecting information by changing readable plaintext into unreadable ciphertext.
Hash / digest: A digital fingerprint of a file that I can compare to see if the file has changed.
Public key: The part of a key pair that is safe to share and can be used for things like verifying a signature or key-based authentication.
Private key: The secret part of a key pair that I must protect and never share or display.
Digital signature: A way to use a private key to sign data so the matching public key can verify the signature and help show whether the data changed.
`authorized_keys`: A file on the SSH server that stores the public keys that are allowed to be used for key-based authentication.
```

## Command-to-Purpose Map

| Command | What it demonstrated |
| --- | --- |
| `openssl enc` | Encrypted and decrypted a file to protect confidentiality. |
| `sha256sum` | Created a digital fingerprint so I could tell if a file changed. |
| `ssh-keygen` | Created a public and private key pair for key-based authentication. |
| `openssl dgst` | Created and verified a digital signature to check integrity and verify the matching key. |
| `ssh ... -o PasswordAuthentication=no` | Forced SSH to use key-based authentication instead of the account password. |

## Safety Rules I Must Remember

```text
Rule 1: Never share, display, screenshot, or upload a private key.
Rule 2: Never share or screenshot passwords or passphrases.
Rule 3: Keep private keys and other cryptographic files protected on the VM and never commit them to GitHub.
```

## Question for the Instructor

```text
My question for the instructor: How can I tell when key-based authentication would be a better choice than password authentication in a real work environment?
```
