# Week 9 Reflection - Vault Exchange Digital Trust

**Student Name:** Dominique D Fleming

**Week:** 9

1. What clicked for you this week?

```text
What clicked for me was how certificates work like digital ID badges. I understand that having a working key is not enough because the certificate helps connect that key to an identity.
```

2. What's still confusing?

```text
I still want to understand more about how browsers decide which root CAs they trust and how that trusted list is managed.
```

3. How does this week's material connect to a cybersecurity career path you're interested in?

```text
This connects to cloud and cybersecurity because I may need to troubleshoot secure connections and certificates. Knowing the difference between a wrong name, wrong CA, and a service that is not running can help me figure out what is actually causing a connection problem.
```

4. One thing you would tell a friend just starting this course:

```text
I would tell them not to just memorize the commands. Focus on understanding what each step is checking and why it passed or failed because that makes the technical stuff much easier to remember.
```

## Professional Growth Check

- [x] I can explain why a working key’s label does not prove who owns it.

- [x] I can read the digital badge and describe the list of signing offices I actually saw.

- [x] I can explain the service’s key, its badge application, the office’s key, and the finished badge.

- [x] I can explain why correct information passed and why an intentionally wrong input was refused.

- [x] I can tell the difference between a service not answering and its badge failing a check.

- [x] I can share useful screenshots while keeping private keys and passwords secret.

## Portfolio Deliverable 3 Reflection

Write 5–7 sentences. What can a correctly checked digital badge tell you about the service? What can it NOT promise? Describe one problem using the message you actually saw, and explain what you checked next. Compare the real website with your practice service. You may start with “I used to think…”, “My check showed…”, and “I now know…”.

```text
I used to think a working key was enough, but now I understand that a correctly checked digital certificate helps connect a public key to the service’s identity. My check showed that the certificate matched the name localhost and came from the Lesson CA that I told the client to accept. A valid certificate does not promise that everything from the service is safe or trustworthy. When I tested the wrong name, I saw “hostname mismatch,” so I checked the SAN and confirmed that the certificate covered localhost instead of wrong.test. The real example.com website used a certificate chain that my browser accepted, while my practice service used a Lesson CA that I manually told OpenSSL to accept. I now know that checking the name, issuer, dates, and trust helps prove I connected to the intended service, but it does not prove everything the service provides is safe.
```
