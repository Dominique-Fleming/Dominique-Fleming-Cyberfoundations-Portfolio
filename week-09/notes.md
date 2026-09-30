# Week 9 Notes - Vault Exchange Digital Trust

**Student Name:** Dominique D  Fleming

**Week:** 9

Use your own words. Short notes and everyday examples are welcome. You do not need to memorize commands.

## From a Key to an Identity

Ivy has two keys with the same label. Why is the label not enough? How can checking a visitor badge help explain the problem?

```text
My explanation: A label on a key is not enough because anyone could give a key a name. A visitor badge helps because it connects a person’s name to something that can be checked. A certificate does something similar by connecting a name to a public key and showing who issued it.
```

## Certificate Fields

A certificate is a digital badge. Explain the name list (SAN), signing office (issuer), start/end dates, public key, signature method, and allowed job (purpose). Which field would you check to see whether the badge covers the right website?

```text
Field and its job: The SAN lists the website names the certificate covers, and this is the field I would check for the right website. The issuer shows which office signed the certificate. The dates show when it can be used, the public key belongs to the service, the signature method shows how it was signed, and the purpose shows what job the certificate is allowed to do.
```

## Issuers and Accepted Trust

Who signed the badge? Who chooses whether to accept the top office? Explain why an office signing its own badge is not enough to make your browser trust it.

```text
My explanation: The issuer signs the certificate, but the browser or client decides which top offices it accepts. An office signing its own certificate does not automatically make it trusted. The browser or client must already accept that office as a trusted root.
```

## Key CSR and Certificate Roles

The CSR is a badge application. Explain the separate jobs of the service’s private key, application, office’s private key, and finished certificate. Who signs the application? Who signs the finished badge?

```text
My explanation: The service’s private key is its secret key and should stay private. The CSR is the badge application, and the service signs it with its private key. The CA uses its own private key to sign the finished certificate. The finished certificate connects the service’s identity to its public key.
```

## TLS and Verification Evidence

Compare looking at a certificate, checking its saved file, and connecting to a running service. TLS sets up a protected connection. What did you observe in each activity?

```text
I looked at: I looked at certificate fields such as the SAN, issuer, dates, public key, purpose, and signature information.
I checked: I checked the saved certificate file using the Lesson CA and the expected name localhost. The correct check passed, while the wrong-name and wrong-CA checks failed for the expected reasons.
I connected to: I connected to the service at 127.0.0.1:8443 using TLS 1.3. The correct connection showed Verification: OK and exit status 0.
```

## Troubleshooting and Remediation

These words mean finding and fixing a problem. Record an actual message, what it meant, and your next step. Consider a wrong name, expired dates, wrong office, canceled certificate, or service that is not running. Label situations you only discussed; do not claim to have tested them.

```text
Message or situation: hostname mismatch
What it means: The certificate was made for localhost, but I told the client to check for wrong.test.
What I would check or fix: I would check the SAN and make sure the expected hostname matches a name listed in the certificate.
Which check I would repeat: I would repeat the hostname verification check using the correct expected name.
```

## Questions I Still Have

```text
A word or step I want explained again: How does a browser decide which root CAs to trust?
```
