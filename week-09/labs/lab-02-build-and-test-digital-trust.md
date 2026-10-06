# Week 9 Lab 02 - Build and Test Digital Trust

**Student Name:** Dominique D Fleming

**Date Completed:** September 29, 2026

**Module:** 3 - Practical Cryptography | **Week:** 9  
**Submission Path:** `week-09/labs/lab-02-build-and-test-digital-trust.md`

> ## Vault Exchange Trust and Key Safety Rule
> Use your assigned Ubuntu training computer (VM) as `analyst`. Keep Week 6–8 files and your SSH login keys unchanged. Do not run `sudo` (administrator commands), change network rules, add your pretend badge office to the computer or browser’s accepted list, or make the practice server available to other computers. All new work stays inside a fresh practice folder under `~/cloud-heights/week9-digital-trust/`.
>
> **Evidence safety:** Never display, upload, or commit private-key contents. Publish selected worksheet text and reviewed screenshots only. Do not upload the whole VM workspace or run `git add .` from it. Crop credentials, access URLs, and account information.

---

## Mission

Ivy needs a digital badge for a practice service running on her training computer. You will help her apply for the badge, issue it from a pretend badge office, and check it.

The digital badge is a **certificate**. The issuing office is a **certificate authority**, shortened to **CA**. You will play both roles for this exercise. Your pretend office will not become trusted by public websites or other people’s browsers.

You will also try a wrong name and a wrong office on purpose. A check refusing the wrong information is the result we want. Finally, you will compare your practice service with the real website from Lab 01.

The badge application is called a **certificate signing request (CSR)**. Signing an application shows that its maker used the matching secret key. It does not prove they have permission to get a badge for someone else’s website. A real issuing office must check that permission before issuing a certificate.

## What You Already Know

Think back to these ideas; you do not need to memorize the technical names:

- A **service** is a program waiting to help another program, like a reception desk waiting for visitors.
- A **VM (virtual machine)** is your assigned training computer, accessed through Cloud Heights.
- A **terminal** is a window where you type instructions, called **commands**. **Bash** reads and runs those instructions.
- **OpenSSL** is the tool we will use to make keys and check certificates.
- A **key pair** has a secret private key and a shareable public key. Never share the private key.
- **TLS** is the method programs use to set up a protected connection. It checks identity and arranges keys to protect the messages.

Looking at a badge, checking a badge, and successfully using it at a door are different activities. We will collect evidence for each one.

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | Assigned Cloud Heights Ubuntu VM, account `analyst`, Bash shell |
| Tools | OpenSSL 3.x, core utilities, `ss`, and two terminal sessions |
| Working root | `~/cloud-heights/week9-digital-trust/` |
| Separate practice area | Fresh `practice-*` folder; no preloaded certificate fixture assumed |
| Practice connection address | `127.0.0.1:8443`, expected DNS name `localhost` |
| Time | 90-120 minutes; work in Parts A-D with pauses |
| Submission | Selected evidence only; private keys stay on the VM |

- [x] I completed Lab 01 or have a dated public-site observation ready.

- [x] I am on my assigned VM and can open a second terminal session to that same VM.

- [x] My account is `analyst` and I can write to the Week 9 workspace.

- [x] I know that an intentional negative test is successful learning evidence when it rejects for the intended reason.

### Cloud Heights Idle Stop

Respond to the Portal's idle warning while actively working. A stopped/deallocated VM is not deleted; restart it from My Lab Environment. Saved files remain, but the TLS listener must be started again after a restart. Reopen your recorded practice path instead of regenerating keys over existing files.

### Before You Type Commands

1. Work in your assigned VM, using the account named `analyst`. Do not paste these commands into your personal computer’s terminal.
2. Copy one entire **bash** box at a time, paste it into the terminal, and press Enter if the last line has not run. Do not copy the box borders or the word `bash`.
3. Wait for your usual command prompt to return before pasting the next box. A **prompt** is the terminal’s “ready for your next command” line. Step 10 is the exception: the service keeps running there.
4. Long commands may wrap onto another screen line. Copy the whole command. You do not need to memorize the options beginning with `-`.
5. Text in **text** boxes is for your worksheet answers, not the terminal. Replace the blank prompts with your own observations.
6. If the result differs from the expected result, stop at that step and show your instructor the command and error. Do not remove checking options to make an error disappear.

A **path** is a file or folder’s address. `~` means your home folder. `cd` means “move into this folder”; `pwd` prints the folder you are currently in. A value such as `W9_RUN` is a named storage box the command uses to remember a path. Keep the commands exactly as written.

## Predict First

Make a best guess for each test. “Expected name” means the name we want the badge to cover. A “listener” is a running program waiting for a connection. Port `8443` is the numbered door this program uses. Explain each prediction in 2–3 sentences; it is okay to be unsure before trying it.

| Test | Your prediction and reason |
| --- | --- |
| Intended lesson CA + expected name `localhost` | I think this test will pass because we are using the correct CA and the expected name localhost. Both should match what the certificate was made for. |
| Same certificate and CA + expected name `wrong.test` | I think this test will fail because wrong.test is not the name we expect the certificate to cover. The CA may be correct, but the name is wrong. |
| Same certificate and expected name + unrelated CA | I think this test will fail because we are checking the certificate with the wrong CA. The name may be correct, but the CA is not the one that issued the certificate. |
| Same intended inputs, but no listener on port 8443 | I think this test will fail because nothing is running and waiting for the connection on port 8443. Even if the certificate information is correct, there is no service there to connect to. |

## Guided Steps

### Part A - Prepare the Key, Request, and Issuer

#### Step 1 - Check the Environment

This first box checks your account, the installed tools, the clock, and whether our numbered door is already in use. It also makes the Week 9 folder if it does not exist. **It does not start the practice service.**

```bash
whoami
openssl version
command -v ss
date -u
mkdir -p ~/cloud-heights/week9-digital-trust
test -w ~/cloud-heights/week9-digital-trust && printf 'Workspace writable\n'
ss -ltn 'sport = :8443'
```
**What you should see, in order**:

1. `analyst` — your account name.
2. An OpenSSL version beginning with `3.` — the tool is installed.
3. A file path ending in `ss` — the tool for listing waiting services is available.
4. Today’s date and time in **UTC**, a shared time standard. It can differ from your local clock by your time-zone offset.
5. `Workspace writable` — you can save files in the Week 9 folder.

```text
Account name shown: analyst
OpenSSL version shown: OpenSSL 3.0.2 15 Mar 2022
Path to the ss tool: /usr/bin/ss
UTC date and time shown: Tue Sep 29 19:51:39 UTC 2026
Workspace writable result: Workspace writable
Port 8443 listener result (blank output means nothing is listening): No service was listed, so nothing is listening on port 8443.
```

6. A heading with no service row below it — port 8443 is free.

**Stop and ask for help** if an item is missing, the date is wrong, or a service row appears. Do not close an unknown program or change the clock yourself.

```text
Account: analyst
OpenSSL version: OpenSSL 3.0.2 15 Mar 2022
UTC clock: Tue Sep 29 19:51:39 UTC 2026
Port 8443 free? Evidence: Yes. The ss command showed the heading but no service underneath it.
```

#### Step 2 - Create a Fresh Practice Folder

This box makes a new folder for this attempt. **Directory** means folder. `mktemp` chooses a fresh name so you do not overwrite earlier work. `umask` restricts who can read newly created files. The four inner folders will hold the badge office files, applications, finished badges, and check results.

```bash
umask 077
W9_RUN=$(mktemp -d "$HOME/cloud-heights/week9-digital-trust/practice-XXXXXX")
cd "$W9_RUN"
mkdir ca requests certificates verification
printf '%s\n' "$PWD"
```

```text
My exact practice path: /home/analyst/cloud-heights/week9-digital-trust/practice-fvzj0I
```

**What you should see:** a path ending in `practice-` plus a random ending. Copy that exact path into your answer above. It will be different for each attempt.

**To resume later:** type `cd`, then a space, then paste your recorded path and press Enter. Type `pwd` and press Enter to confirm you are in that folder. Do not run Step 2 again just to resume; that would create a different attempt.

#### Step 3 - Create the Service Key and CSR

First make a new key pair for Ivy’s practice service. **RSA** is the kind of key we are making; **2048** is its size in bits. These values are provided for you. This box saves the private key and writes a separate file containing only the public key. Do not reuse a key you use to log in.

```bash
openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out requests/service.key.pem
chmod 600 requests/service.key.pem
openssl pkey -in requests/service.key.pem -pubout -out requests/service.public.pem
```
**What you should see:** progress symbols may appear, then the prompt returns. Some lines finish without printing anything. The private-key file is restricted to your account by `chmod 600`. It has no password protection for this temporary practice exercise; real systems need a planned way to protect their keys.

**Do not open or print `service.key.pem`.** `cat` displays a file’s contents, so use it only on the exact public-information files listed in these instructions.

Next make the badge application: the **CSR**. It includes the service’s public key, its requested name, and a signature made with its private key. `localhost` is the practice name for this computer. **SAN** means the list of names the certificate should cover. This request asks for `localhost`. The box also checks the request’s signature and displays the request, which contains public information.

```bash
openssl req -new -sha256 -key requests/service.key.pem -out requests/service.csr.pem -subj "/O=CyberFoundations Lab/CN=localhost" -addext "subjectAltName=DNS:localhost"
openssl req -in requests/service.csr.pem -noout -verify
openssl req -in requests/service.csr.pem -noout -text > verification/csr-inspection.txt
cat verification/csr-inspection.txt
```
Expected: a request-signature verification message such as `Certificate request self-signature verify OK`, plus requested `DNS:localhost` in the inspection. A CSR contains public information and a signature, not the private key. Record the actual output, not this example.

**Capture now (Step 3):** Save `week09-lab02-csr-check.png` showing the public CSR inspection and passing signature check; never capture private key contents.

```text
Requested subject and SAN: O = CyberFoundations Lab, CN = localhost
SAN = DNS:localhost
CSR signature-check result: Certificate request self-signature verify OK
What the successful signature check tells me about the request: It tells me the CSR was signed using the private key that matches the public key in the request.
Why signing an application does not prove permission to use someone else’s website name: The signature only proves the matching private key was used. It does not prove the person has permission to request a certificate for someone else’s website.
```

#### Step 4 - Model the Issuing Office

Now pretend you work at the badge office. The office needs its **own** key pair, separate from the service’s key. This box makes that pair and the office’s certificate. The office signs its own certificate; that is called **self-signed**. Printing and signing your own badge does not make other people accept it. Later, we will tell our checking tool to accept this particular office for a particular test.

```bash
openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out ca/lesson-ca.key.pem
openssl req -new -x509 -sha256 -days 365 -key ca/lesson-ca.key.pem -out ca/lesson-ca.cert.pem -subj "/O=CyberFoundations Lab/CN=Week 9 Lesson CA" -addext "basicConstraints=critical,CA:TRUE,pathlen:0" -addext "keyUsage=critical,keyCertSign,cRLSign"
chmod 600 ca/lesson-ca.key.pem
openssl x509 -in ca/lesson-ca.cert.pem -noout -subject -issuer -dates
```
**What you should see:** Subject and Issuer both name `Week 9 Lesson CA`, followed by dates. That is expected for this self-signed office certificate. Here the office will sign the service badge directly; there is no middle office (intermediate CA). Your browser and computer’s accepted-office lists have not changed.

```text
Lesson CA subject shown: O = CyberFoundations Lab, CN = Week 9 Lesson CA
Lesson CA issuer shown: O = CyberFoundations Lab, CN = Week 9 Lesson CA
Lesson CA validity dates: Sep 29 20:07:02 2026 GMT to Sep 29 20:07:02 2027 GMT
Why a self-signed office certificate is not automatically accepted: A self-signed certificate is not automatically trusted because the office signed its own certificate. The program checking it still has to choose to accept that office as trusted.
```

**Pause point:** save your worksheet and practice path before Part B. Your keys and request are saved on the VM.

### Part B - Issue, Inspect, and Verify

#### Step 5 - Set the Badge Rules and Issue the Certificate

This box writes the office’s rules into a small settings file. The rules say: “This badge is for a service called `localhost`, for use as a web server. It cannot issue other badges.”

Copy the entire box, including the last `EOF` on its own line. `EOF` tells the terminal “the file text ends here.” If you see a `>` prompt while entering the block, the terminal is waiting for the remaining lines. Do not paste the next command until the complete block has finished.

You do not need to memorize these settings. `CA:FALSE` means “not an issuing office”; `serverAuth` means “may identify a server”; `DNS:localhost` is the allowed name. The office chooses the final rules instead of accepting everything an applicant might ask for.

```bash
cat > ca/server-ext.cnf <<'EOF'
[server_cert]
basicConstraints=critical,CA:FALSE
keyUsage=critical,digitalSignature,keyEncipherment
extendedKeyUsage=serverAuth
subjectAltName=DNS:localhost
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer
EOF
```
**What you should see:** the usual prompt returns; this settings-file step normally prints nothing.

Now issue the badge. The next box uses the **office’s private key** to sign the certificate, gives it a 30-day date period, saves it, and displays it. Its serial number is a randomly chosen tracking number; yours need not match another student’s.

```bash
openssl x509 -req -in requests/service.csr.pem -CA ca/lesson-ca.cert.pem -CAkey ca/lesson-ca.key.pem -set_serial "0x$(openssl rand -hex 16)" -days 30 -sha256 -extfile ca/server-ext.cnf -extensions server_cert -out certificates/service.cert.pem
openssl x509 -in certificates/service.cert.pem -noout -text > verification/certificate-inspection.txt
cat verification/certificate-inspection.txt
```
**What you should see:** Subject includes `localhost`; Issuer includes `Week 9 Lesson CA`; SAN includes `DNS:localhost`; Basic Constraints says `CA:FALSE`; and Extended Key Usage lists TLS Web Server Authentication. If a required value differs, stop and ask for help before the next step.

**Capture now (Step 5):** Save `week09-lab02-issued-certificate.png` showing issued public certificate fields.

```text
SAN shown in the certificate inspection: DNS:localhost
Basic Constraints shown in the certificate inspection: CA:FALSE
Extended Key Usage shown in the certificate inspection: TLS Web Server Authentication
Any difference from the expected result (write "none" if there is none): none
```

Use these reminders to fill the table: **Subject** describes the badge holder; **Issuer** names the signing office; **SAN** lists allowed names; **Not Before/Not After** give the start/end dates; **algorithm** means calculation method; **Basic Constraints** says whether this is an issuing office; **Key Usage/EKU** describe allowed jobs. The website’s key and the office’s signature have different jobs.

Your dates, tracking number, and key details will differ from classmates’ files. Copy your finished certificate’s values, not just the application’s values.

| Field | Issued value | Why it matters |
| --- | --- | --- |
| Subject | O = CyberFoundations Lab, CN = localhost | Tells me who the certificate says it belongs to. |
| Issuer | O = CyberFoundations Lab, CN = Week 9 Lesson CA | Tells me which office signed the certificate. |
| SAN | DNS:localhost | Tells me the name the certificate covers. |
| Not Before / Not After | Sep 29 20:13:07 2026 GMT / Oct 29 20:13:07 2026 GMT | Tells me when the certificate starts and expires. |
| Subject key algorithm and size | rsaEncryption, 2048 bit | Tells me the type and size of the service's public key. |
| Certificate signature algorithm | sha256WithRSAEncryption | Tells me the method the CA used to sign the certificate. |
| Basic Constraints | CA:FALSE | Tells me this certificate cannot issue other certificates. |
| Key Usage / Extended Key Usage | Digital Signature, Key Encipherment / TLS Web Server Authentication | Tells me what jobs the certificate is allowed to do. |

#### Step 6 - Check That the Same Public Key Was Carried Through

Did the public key from Ivy’s key pair make it into the application and then the finished badge? This box copies out the public information and calculates a **SHA-256 fingerprint** for each public-key file. A fingerprint is a calculated label we can compare. This box does not display private keys.

```bash
openssl req -in requests/service.csr.pem -pubkey -noout > requests/csr.public.pem
openssl x509 -in certificates/service.cert.pem -pubkey -noout > certificates/service.public.pem
sha256sum requests/service.public.pem requests/csr.public.pem certificates/service.public.pem
```
**What you should see:** three lines beginning with the same long string of letters and numbers. Those strings are the fingerprints, also called **digests**. Matching strings show that the same public key appears in the original public-key file, the CSR, and the certificate. They do not tell us whether the name, dates, or issuing office should be accepted. If the strings differ, stop and ask for help before continuing.

```text
Fingerprint for requests/service.public.pem: 63ce70057424616e20d47c0d4482ab23236c474c92570a7950c8564651765d0a
Fingerprint for requests/csr.public.pem: 63ce70057424616e20d47c0d4482ab23236c474c92570a7950c8564651765d0a
Fingerprint for certificates/service.public.pem: 63ce70057424616e20d47c0d4482ab23236c474c92570a7950c8564651765d0a
Do all three match, and what does that show: Yes, all three fingerprints match. This shows that the same public key was used in the original key pair, the CSR, and the finished certificate.
```

**Capture now (Step 6):** Save `week09-lab02-key-correspondence.png` while all three matching public-key fingerprints and filenames are visible. Do not capture private key contents.

#### Step 7 - Run the Passing Verification

Now check the saved certificate without connecting to a running service. This is an **offline check**: it reads files on your VM.

Read the main choices in the command before running it:

| Command part | What we are asking |
|---|---|
| `-CAfile ca/lesson-ca.cert.pem` | Accept this badge office for this check. This chosen starting point is called a trust anchor. |
| `-no-CApath -no-CAstore` | Do not also use the computer’s default certificate folders or store. |
| `-purpose sslserver` | Check that the badge is suitable for a server. |
| `-verify_hostname localhost` | Check that it covers the name `localhost`. |

OpenSSL also uses the computer’s clock to check dates. The last three lines save the result number, show the message, and print that number. An **exit status** of `0` means the command succeeded; another number means it did not. Read the message to learn why. `> ... 2>&1` saves the command’s messages in a text file; it is included for you.

```bash
openssl verify -CAfile ca/lesson-ca.cert.pem -no-CApath -no-CAstore -purpose sslserver -verify_hostname localhost certificates/service.cert.pem > verification/pass.txt 2>&1
W9_STATUS=$?
cat verification/pass.txt
printf 'Exit status: %s\n' "$W9_STATUS"
```
**What you should see:** `certificates/service.cert.pem: OK` and exit status `0`. This is our working starting test, also called the **baseline**. If it fails, stop and ask for help. The later wrong-input tests are meaningful only after this starting test works.

**Capture now (Step 7):** Save `week09-lab02-verify-pass.png` showing passing offline verification and exit status `0`.

```text
Command message shown by verification/pass.txt: certificates/service.cert.pem: OK
Exit status printed: 0
What this baseline result proves: The certificate passed when I used the intended CA, the expected name localhost, and the server purpose.
```

#### Step 8 - Ask for the Wrong Name on Purpose

Keep the same badge and office, but ask whether the badge covers `wrong.test`. It was issued for `localhost`, so these names should not match. Copy this complete box; the change is already made for you.

```bash
openssl verify -CAfile ca/lesson-ca.cert.pem -no-CApath -no-CAstore -purpose sslserver -verify_hostname wrong.test certificates/service.cert.pem > verification/wrong-name.txt 2>&1
W9_STATUS=$?
cat verification/wrong-name.txt
printf 'Exit status: %s\n' "$W9_STATUS"
```
Expected: hostname mismatch and a nonzero exit status. The leaf and CA did not change. `wrong.test` is just an expected-name input; this offline command makes no connection to that name.

**Capture now (Step 8):** Save `week09-lab02-verify-wrong-name.png` showing the intended wrong-name rejection and nonzero status.

```text
Command message shown by verification/wrong-name.txt: error 62 at 0 depth lookup: hostname mismatch
error certificates/service.cert.pem: verification failed
Exit status printed: 2
Why the check refused this name: The certificate was made for localhost, not wrong.test. Since the name did not match, OpenSSL refused the certificate.
```

#### Step 9 - Ask a Different Office to Vouch for the Same Badge

First make a second pretend office with its own key. It did not issue our service’s badge. This is a **negative test**: we intentionally give wrong information to see whether the check refuses it. The first box makes the second office; the next box checks the unchanged service certificate using only that second office.

```bash
openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out ca/unrelated-ca.key.pem
openssl req -new -x509 -sha256 -days 365 -key ca/unrelated-ca.key.pem -out ca/unrelated-ca.cert.pem -subj "/O=CyberFoundations Lab/CN=Unrelated Lesson CA" -addext "basicConstraints=critical,CA:TRUE,pathlen:0" -addext "keyUsage=critical,keyCertSign,cRLSign"
chmod 600 ca/unrelated-ca.key.pem
```

```bash
openssl verify -CAfile ca/unrelated-ca.cert.pem -no-CApath -no-CAstore -purpose sslserver -verify_hostname localhost certificates/service.cert.pem > verification/wrong-ca.txt 2>&1
W9_STATUS=$?
cat verification/wrong-ca.txt
printf 'Exit status: %s\n' "$W9_STATUS"
```
**What you should see:** a message such as `unable to get local issuer certificate`, and an exit status other than `0`. In plain language: “The office you gave me cannot vouch for this badge.” That refusal is expected. A missing-file error is a different problem; ask for help if you see one. Do not install these offices into your browser or computer’s accepted list.

**Capture now (Step 9):** Save `week09-lab02-verify-wrong-ca.png` showing intentional wrong-CA rejection and nonzero status.

```text
Command message shown by verification/wrong-ca.txt: error 20 at 0 depth lookup: unable to get local issuer certificate
error certificates/service.cert.pem: verification failed
Exit status printed: 2
Why the unrelated office could not vouch for this badge: The unrelated CA did not sign the service certificate. Since I told OpenSSL to trust only that unrelated CA, it could not verify the certificate.
```

**Pause point:** record all three results below before Part C. Keep the same service certificate throughout these tests.

| Test | Leaf file | CA file | Expected name | Actual result / exit status | Explanation |
| --- | --- | --- | --- | --- | --- |
| Passing baseline | certificates/service.cert.pem | ca/lesson-ca.cert.pem | localhost | OK / 0 | The correct CA and name were used, so the check passed. |
| Wrong name | certificates/service.cert.pem | ca/lesson-ca.cert.pem | wrong.test | hostname mismatch / 2 | The certificate covers localhost, not wrong.test. |
| Wrong CA | certificates/service.cert.pem | ca/unrelated-ca.cert.pem | localhost | unable to get local issuer certificate / 2 | The unrelated CA did not sign the service certificate. |

### Part C - Try a Protected Connection on This VM

So far we checked saved files. Now we will connect two programs on the same VM.

- The **server** waits for a visitor. The **client** makes the visit and checks the badge.
- `127.0.0.1` is the address meaning “this same computer.” Connecting back to yourself is called **loopback**.
- `8443` is the numbered door, or **port**, where our server waits.
- `localhost` is the name we expect on its certificate. An address tells the client where to go; the expected name tells it which identity to check.
- **TLS 1.3** is the protected-connection version we will use. Its handshake is the initial exchange where the programs check identity and arrange message-protection keys.

Keep two terminal windows open: **A runs the server; B runs the checks.** Both must connect to your assigned VM.

#### Step 10 - Start the Listener in Terminal A

Use your existing terminal as **Terminal A**. Make sure you are in the practice folder from Step 2. This box starts your waiting service:

```bash
openssl s_server -accept 127.0.0.1:8443 -cert certificates/service.cert.pem -key requests/service.key.pem -www -tls1_3
```
**What you should see:** `ACCEPT`, and the usual prompt does not return. This is normal: the server is waiting. No client has checked its identity yet. Leave this window alone and continue in Terminal B. Keep the address exactly `127.0.0.1` so the service is available only on this VM. Do not change it to `0.0.0.0` or change network rules.

```text
Message shown in Terminal A after starting the listener: ACCEPT
Time you started the listener: 4:40pm EST
```

#### Step 11 - Check the Connection in Terminal B

Open a second terminal to the **same VM**. Use `cd` with the exact practice path you recorded in Step 2. Confirm it with `pwd`; the `ca/` and `certificates/` directories must belong to that run. Then execute:

```bash
ss -ltn 'sport = :8443'
```
Expected: a listener at `127.0.0.1:8443`. Save that evidence before the connection test.

**Capture now (Step 11):** Save `week09-lab02-loopback-listener.png` showing the `ss` row for loopback `127.0.0.1:8443` only.

```text
Listener row shown by ss: 127.0.0.1:8443
```

```bash
openssl s_client -connect 127.0.0.1:8443 -servername localhost -CAfile ca/lesson-ca.cert.pem -no-CApath -no-CAstore -verify_hostname localhost -verify_return_error -tls1_3 -brief < /dev/null > verification/tls-pass.txt 2>&1
W9_STATUS=$?
cat verification/tls-pass.txt
printf 'Exit status: %s\n' "$W9_STATUS"
```
**What you should see:** TLS 1.3, `Verification: OK`, and exit status `0`. If not, confirm Terminal A is still waiting and both terminals use the same practice folder. Ask for help if it still differs.

**Capture now (Step 11):** Save `week09-lab02-tls-pass.png` immediately after passing TLS with `Verification: OK` and status `0`.

Two command options have different jobs. `-servername localhost` tells the server which service name we want; this label is called **SNI**. `-verify_hostname localhost` checks whether the certificate actually covers the name. Requesting a name is not the same as checking it. `-verify_return_error` tells the client to stop if the certificate check fails. The short output also shows a **cipher**: the chosen method for protecting messages.

Now change only the expected name, keeping SNI and all other inputs the same:

```bash
openssl s_client -connect 127.0.0.1:8443 -servername localhost -CAfile ca/lesson-ca.cert.pem -no-CApath -no-CAstore -verify_hostname wrong.test -verify_return_error -tls1_3 -brief < /dev/null > verification/tls-wrong-name.txt 2>&1
W9_STATUS=$?
cat verification/tls-wrong-name.txt
printf 'Exit status: %s\n' "$W9_STATUS"
```
**What you should see:** a certificate/name mismatch message and a status other than `0`. Reaching the server’s door is not enough; the badge must also pass the name check. If the message says “connection refused,” the server is not waiting, so that is not evidence of the intended name rejection.

**Capture now (Step 11):** Save `week09-lab02-tls-wrong-name.png` immediately after intended wrong-name TLS rejection with nonzero status.

```text
Listener address and port: 127.0.0.1:8443
Connected destination: 127.0.0.1:8443
Expected identity for the passing test: localhost
SNI value: localhost
CA file used: ca/lesson-ca.cert.pem
Negotiated TLS version and cipher from the passing output: TLSv1.3 with TLS_AES_256_GCM_SHA384
Passing verification message and exit status: Verification: OK — Exit status: 0
Failing verification message and exit status: hostname mismatch / certificate verify failed — Exit status: 1
What changed between the two tests: I changed only the expected hostname from localhost to wrong.test. Everything else stayed the same.
Explain each job: the certificate links a name to a public key; the handshake checks use of the matching private key; newly arranged traffic keys protect messages. How are these different? The certificate connects the name localhost to the public key. The handshake checks that the server has the matching private key. After that, TLS creates new traffic keys that protect the messages sent during the connection.
```

#### Step 12 - Stop Only Your Listener

In Terminal A, press **Ctrl+C**. In Terminal B, rerun `ss -ltn 'sport = :8443'`; no listener row should remain. Do not use a broad process-kill command.

In Terminal B, use the original passing inputs but save this stopped-listener result in its own file:

```bash
openssl s_client -connect 127.0.0.1:8443 -servername localhost -CAfile ca/lesson-ca.cert.pem -no-CApath -no-CAstore -verify_hostname localhost -verify_return_error -tls1_3 -brief < /dev/null > verification/no-listener.txt 2>&1
W9_STATUS=$?
cat verification/no-listener.txt
printf 'Exit status: %s\n' "$W9_STATUS"
```

**What you should see:** “connection refused” and a status other than `0` when no service is waiting. There is no one at the door, so the certificate check never gets that far. Keep this result separate from the earlier wrong-badge result.

```text
ss output after you pressed Ctrl+C: No listener row was shown for port 8443.
Message shown by verification/no-listener.txt: Connection refused / connect:errno=111
Exit status printed: 1
Why this failure is different from a badge failing its check: This failure happened because no service was listening on port 8443, so the connection could not start. In the wrong-name test, the server was running, but the certificate failed because the name did not match. Here, the certificate check never happened because nobody was at the door.
```

**Pause point:** the server is now stopped. Save your worksheet and screenshots before Part D.

### Part D - Explain Problems and Collect Your Work

#### Step 13 - Interpret Four Conditions

For each row, explain what went wrong, what message or field you would look at, what you would correct, and which check you would repeat. **Retest** means try the check again after a correction. Use your earlier wrong-name, wrong-office, and stopped-server results to reason about rows 1, 3, and 4. Row 2 is a pretend situation to explain in writing; do not change the VM clock or certificate dates.

| Condition | Which check cannot pass? | What would you look at? | What would you fix, and why? | Which check would you repeat? |
| --- | --- | --- | --- | --- |
| `localhost` is absent from a service's SAN | Hostname check | Look at the SAN and the hostname mismatch message. | Get a certificate that includes localhost in the SAN so the name matches. | Repeat the hostname verification check. |
| Not After is in the past relative to the correct clock | Date/validity check | Look at the Not After date and the correct system time. | Renew the expired certificate and deploy the new one so the server uses a valid certificate. | Repeat the certificate verification check. |
| An unrelated CA file is supplied | Trust/issuer check |   Look for unable to get local issuer certificate and check which CA file was supplied. | Use the correct CA file that issued the certificate so the trust chain can be verified. | Repeat the CA verification check. |
| No listener exists at `127.0.0.1:8443` | Connection check | Look for Connection refused and use ss to see if anything is listening on port 8443. | Start the correct service on 127.0.0.1:8443 because the client cannot connect if nobody is listening. | Repeat the ss check, then repeat the TLS connection check. |

A badge may need replacing when its dates run out. Getting a new certificate is called **renewal**. The service must also be set up to use the new file; that is **deployment**. An office can cancel a certificate before its end date; that is **revocation**. Our file checks did not contact an online cancellation service.

In your own words, explain why getting a replacement file is not enough until the server uses it. Then explain why our passing file check does not prove that someone checked an up-to-date cancellation list.

```text
Getting a replacement certificate is not enough because the server could still be using the old certificate. The new certificate has to be deployed so the server actually uses it. After deployment, I would test the connection again to make sure the new certificate is being presented.

Our passing file check does not prove that an up-to-date revocation list was checked because the test only used the certificate and CA file we supplied. It did not contact an online revocation service. The certificate passed our checks, but that does not prove it had not been revoked somewhere else.
```

#### Step 14 - Compare the Real Website with Your Practice Service

Use your dated Lab 01 observation and your actual local certificate.

| Comparison | Public website from Lab 01 | Isolated lesson service |
| --- | --- | --- |
| Intended hostname / SAN | example.com / SAN included example.com and *.example.com | localhost / SAN was DNS:localhost |
| Issuer and chain roles | example.com → Cloudflare TLS Issuing ECC CA 3 → SSL.com TLS Transit ECC CA R2 → SSL.com TLS ECC Root CA 2022 | localhost certificate → Week 9 Lesson CA. The Lesson CA acted as our practice issuing office. |
| Validity interval |  Sep. 26, 2026 – Dec. 25, 2026 | Sep. 29, 2026 – Oct. 29, 2026 |
| Allowed purpose | Website/server certificate used for a secure HTTPS connection | TLS Web Server Authentication |
| Who accepts the top issuing office, and how is that choice made? | The browser accepts a root CA from its trusted list/trust store. | I manually chose to trust our Lesson CA by supplying ca/lesson-ca.cert.pem to OpenSSL. I did not add it to the browser/system trusted list. |
| Where does the service run, and how did you connect? | Public website reached through the browser at example.com | On my practice VM at 127.0.0.1:8443; I connected locally with openssl s_client. |
| Evidence actually observed | Certificate subject, SAN, issuer, validity dates, fingerprint, browser acceptance, and displayed certificate chain | Certificate fields, SAN localhost, matching public-key fingerprints, Verification: OK, TLS 1.3 connection, wrong-name rejection, wrong-CA rejection, and stopped-listener refusal |
| What does this evidence NOT tell you? | It does not prove everything on the website is safe or trustworthy, and I did not independently prove which chain certificates the server itself sent. | It does not prove public/browser trust or that revocation was checked online. It also does not make our practice CA publicly trusted. |

## Stop & Check

- Can you explain the separate jobs of the service’s secret key, its application (CSR), the office’s secret key, and the finished badge (certificate)?
- Does the issued SAN contain the intended name?
- Do all three public-key fingerprints match, with no private key displayed?
- Did the correct-input check pass before you tried the wrong inputs?
- Did each failure occur for the predicted reason rather than because of a missing file?
- Did the service wait only at `127.0.0.1:8443`, and did you stop it afterward?

## Test

Your evidence must show: three matching public-key fingerprints; a passing file check; refusal of the wrong name; refusal of the wrong office; a passing live TLS connection; refusal of the wrong name during TLS; and a refused connection after stopping the service. For each check, include the command you ran, the message, and the exit-status number. A number other than `0` is not enough by itself: explain the message. Do not copy the terminal’s prompt into a command.

**Capture now (Step 12):** Save `week09-lab02-no-listener.png` showing the stopped listener (`ss` with no listener), connection refusal and nonzero status.

## Capture Evidence

Capture each named result while visible, with commands and status together when possible. Do not screenshot private keys, credentials, access URLs or unfiltered verbose TLS session dumps. Copy relevant result text into the worksheet too.

**Upload to your own GitHub portfolio (not the VM):** Save exact filenames on your computer and review each image for legibility and privacy. In your connected portfolio repository open `assets/screenshots/week-09/`. If missing, from the repository root choose **Add file > Create new file** named `assets/screenshots/week-09/README.md`, add a description and **Commit changes**. Inside that folder choose **Add file > Upload files**, select reviewed images and **Commit changes**. Do not change repository visibility. Open each committed image in **Raw** or image view, right-click and **Copy image address**. Add its direct `https://` address and caption in the matching **Screenshot Links and Captions** box. Do not paste a GitHub file-view webpage (`github.com/.../blob/...`), local path or repository-relative path. Private images may not preview in the portal: check them in your own repository without making it public. Existing valid saved addresses stay valid. A relative link such as `../../assets/screenshots/week-09/week09-lab01-certificate-fields.png` is for manually edited GitHub Markdown only, not these portal boxes.

**Order:** Capture while visible; save and review; upload and commit images; add direct links and captions; finish answers; **Save Progress**; then **Submit to GitHub**. Save Progress stores portal answers. Submit to GitHub commits worksheet Markdown and references, **not image bytes**. Inspect committed Markdown and images. If links or answers change later, Save Progress and Submit to GitHub again.

## Explain

Write 5–7 sentences telling the story of what you did. Start with “First I made…”, then describe the badge application, the issuing office’s signature, your inspection, your file checks, and the live connection. Say which office you told the client to accept. Include one wrong-input test, why it failed, and what you kept the same.

```text
First I made a private key and public key for my practice service. Then I made a CSR, which was like a badge application asking for a certificate for localhost. My Week 9 Lesson CA acted as the issuing office and used its private key to sign the certificate. I inspected the certificate and confirmed that the SAN included localhost and that the public keys matched. I checked the certificate files and then made a live TLS connection to 127.0.0.1:8443, telling the client to accept the Week 9 Lesson CA. I also changed only the expected hostname from localhost to wrong.test while keeping the CA, certificate, SNI, and connection the same, and the test failed because the certificate did not match wrong.test.
```

## Required Evidence

Upload and commit these exact filenames in your own GitHub portfolio under `assets/screenshots/week-09/` (not on the VM), then add direct image addresses and captions below:

- `week09-lab02-csr-check.png`
- `week09-lab02-issued-certificate.png`
- `week09-lab02-key-correspondence.png`
- `week09-lab02-verify-pass.png`
- `week09-lab02-verify-wrong-name.png`
- `week09-lab02-verify-wrong-ca.png`
- `week09-lab02-loopback-listener.png`
- `week09-lab02-tls-pass.png`
- `week09-lab02-tls-wrong-name.png`
- `week09-lab02-no-listener.png`

Add numbered image suffixes if necessary for readability. Keep `verification/*.txt` on the VM for your own reference; publishing those logs is optional only after a content review. Do not upload any `.key.pem` file, even though it is a classroom key.

### Screenshot Links and Captions (required)

![week09-lab02-csr-check.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-09/week09-lab02-csr-check.png)

**Caption - CSR inspection:** Shows that my CSR signature passed verification and that the requested identity included localhost.

![week09-lab02-issued-certificate.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-09/week09-lab02-issued-certificate.png)

**Caption - issued certificate:** Shows my issued localhost certificate, including its issuer, validity dates, SAN, and allowed server purpose.

![week09-lab02-key-correspondence.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-09/week09-lab02-key-correspondence.png)

**Caption - public-key fingerprints:** Shows that all three public-key fingerprints match, confirming the same public key carried through the service key, CSR, and issued certificate.

![week09-lab02-verify-pass.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-09/week09-lab02-verify-pass.png)

**Caption - passing offline check:** Shows my certificate passed the offline verification using the Lesson CA, localhost hostname, and server purpose with exit status 0.

![week09-lab02-verify-wrong-name.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-09/week09-lab02-verify-wrong-name.png)

**Caption - wrong-name refusal:** Shows my offline verification failed with a hostname mismatch when I changed the expected name to wrong.test while keeping the certificate and Lesson CA the same.

![week09-lab02-verify-wrong-ca.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-09/week09-lab02-verify-wrong-ca.png)

**Caption - wrong-office refusal:** Shows my offline verification failed because the unrelated CA could not verify the service certificate while the certificate and localhost hostname stayed the same.

![week09-lab02-loopback-listener.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-09/week09-lab02-loopback-listener.png)

**Caption - loopback listener:** Shows my TLS service listening only on the loopback address at 127.0.0.1:8443.

![week09-lab02-tls-pass.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-09/week09-lab02-tls-pass.png)

**Caption - passing TLS connection:** Shows my live TLS 1.3 connection successfully verified localhost using the Lesson CA with exit status 0.

![week09-lab02-tls-wrong-name.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-09/week09-lab02-tls-wrong-name.png)

**Caption - TLS wrong-name refusal:** Shows my live TLS connection failed with a hostname mismatch when I changed the expected name to wrong.test while keeping the SNI, CA, and connection the same.

**Caption - stopped listener:**

### Screenshot Links and Captions - Optional Extra Evidence (leave blank if you do not need it)

**Caption - extra image 1:** Shows the lower certificate fields, including CA:FALSE, TLS Web Server Authentication, and the DNS:localhost SAN.

![week09-lab02-extra-02.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-09/week09-lab02-extra-01.png)

**Caption - extra image 2:**

## Analysis Questions

**Analysis Question 1.** Who signs the badge application: the service or the office? Who signs the finished badge? Why does signing an application not prove you have permission to use someone else’s website name? Write at least 3 sentences.

```text
The service signs the badge application (CSR) with its private key to show it has the matching key. The issuing office (CA) signs the finished certificate with the CA’s private key. Signing the application does not prove I have permission to use someone else’s website name because it only proves I have the private key that matches the public key in the application. The CA still needs to check that I am actually allowed to use the website name I requested.
```

**Analysis Question 2.** You kept the same certificate but changed the name or office used for the check. Why did the answer change? Use the messages from both wrong-input tests. Write at least 3 sentences.

```text
The answer changed because each test checked a different part of trust, even though I kept the same certificate. When I changed the expected name to `wrong.test`, I got a “hostname mismatch” because the certificate was made for `localhost`. When I used the unrelated CA, I got “unable to get local issuer certificate” because that CA did not sign my certificate. These results showed that both the correct name and the correct issuing office matter when checking a certificate.
```

**Analysis Question 3.** Explain the difference between a server waiting at a door, a badge passing its checks, and messages traveling through a protected connection. If you see “connection refused,” what would you check first, and why? Write at least 3 sentences.

```text
A server waiting at a door means the service is running and listening on the correct address and port. A badge passing its checks means the certificate has the correct name, dates, and trusted issuing office. A protected connection means TLS has created traffic keys to protect the messages traveling between the client and server. If I see “connection refused,” I would first check if the service is listening on the correct address and port because the certificate cannot be checked if nobody is waiting at the door.
```

## Submission Checklist

- [x] Fresh practice path recorded; no previous files or SSH configuration changed

- [x] Dedicated service key and CSR created; issuer role explained

- [x] Issued certificate fields and matching public components recorded

- [x] Offline success, wrong-name failure, and wrong-CA failure explained

- [x] Loopback listener address and passing/failing TLS evidence captured

- [x] Listener stopped; connection failure distinguished from certificate failure

- [x] Four-condition diagnosis and public/local comparison completed

- [x] Analysis questions and walkthrough written in my own words

- [x] Lab 01, notes, and reflection included for Portfolio Deliverable 3

- [x] No private keys, credentials, or access URLs in the submission

- [x] Worksheet committed to `week-09/labs/lab-02-build-and-test-digital-trust.md`

## GitHub / Lab Portal Submission

Your **portfolio repository** is your course-work folder on GitHub. A **commit** saves a set of changes there. Use the editing or upload method you practiced in Week 7; ask for a demonstration if you need one. Upload only the listed documents and reviewed images, never the whole VM folder.

1. Capture each required result at its named step, save exact filenames on your computer and review for legibility and privacy.
2. In your own GitHub portfolio open `assets/screenshots/week-09/`, choose **Add file > Upload files** for reviewed images and **Commit changes**. Create the folder first as described above if needed.
3. Open each committed image in Raw/image view; add direct image addresses and captions in the matching portal boxes and finish your answers.
4. Press **Save Progress** to store portal answers, then **Submit to GitHub** to commit worksheet Markdown and references only, not image bytes. If anything changes later, save and submit again. Complete the Week 9 Notes and Reflection worksheets in the portal the same way; use the submissions guide for Portfolio Deliverable 3.
5. Open the committed Markdown and every image on GitHub to confirm formatting and privacy.

*CyberVisionaries Institute · CyberFoundations · Tier I*
