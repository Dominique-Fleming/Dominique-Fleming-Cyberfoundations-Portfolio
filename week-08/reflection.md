# Week 8 Reflection — Practical Cryptography

**Student Name:** Dominique D  Fleming

**Date:** September 27, 2026

Answer each question in 3–5 sentences.

1. How are encryption and hashing different?

```text
Encryption protects confidentiality by changing readable plaintext into unreadable ciphertext using a key. The encrypted information can be decrypted and made readable again with the correct key or passphrase. Hashing creates a digest, or digital fingerprint, that can be compared to see if a file has changed. Unlike encryption, hashing is not meant to be reversed back into the original information.
```

2. How are symmetric and asymmetric cryptography different?

```text
Symmetric cryptography uses one shared secret key to encrypt and decrypt information. Asymmetric cryptography uses a key pair, with a public key that can be shared and a private key that must stay protected. The two keys have different jobs but work together. The biggest difference is **one shared key vs. a public and private key pair**.
```

3. Why does a private key need stronger protection than a public key?

```text
A private key needs stronger protection because it is meant to stay secret and only be accessible to its owner. If someone else gets the private key, they could potentially use it to authenticate or create digital signatures using that key. A public key is different because it is designed to be shared when needed. That is why the public key can be distributed, but the private key must stay protected.
```

4. What changed between Week 6 password-based SSH and Week 8 key-based SSH? What stayed the same?

```text
In Week 6, I used a password to authenticate and connect to the VM through SSH. In Week 8, I used key-based authentication, where my private key matched the public key stored in `authorized_keys`. What changed was the authentication method used to prove I was allowed access. What stayed the same was that I was still using SSH to securely connect to the VM.
```

5. Which Week 8 task felt most connected to a real cybersecurity job, and why?

```text
The Week 8 task that felt most connected to a real cybersecurity job was using key-based SSH authentication. It showed me how a public and private key can be used instead of relying only on a password to access a system. I also had to make sure the private key stayed protected while the public key was placed in `authorized_keys`. This felt realistic because protecting credentials and securely accessing systems are important parts of cybersecurity work.
```

6. What question do you have about certificates, identity, or trust before Week 9?

```text
One question I have before Week 9 is: How do certificates prove that a website or system is really who it claims to be? I understand how public and private keys work, but I want to learn how certificates connect those keys to a trusted identity. I also want to understand who decides whether a certificate should be trusted.
```
