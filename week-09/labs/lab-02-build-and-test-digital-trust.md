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

You will create and check an identity for a practice service on your assigned training computer. Think of a service badge, an application for that badge, and an issuing office. We will introduce their real technical names at the action where you use them.

The analogy has limits: a digital signature is a mathematical check, not a handwritten mark; a certificate is public information, not the secret that protects messages. A real issuing office must check permission to use a name. You play both roles in this isolated exercise, which does not make your issuing office trusted by public websites or browsers.

A **VM (virtual machine)** is your assigned training computer. A **terminal** is a window for commands; **Bash** reads those commands. **OpenSSL** is the installed tool we use. You do not need prior OpenSSL knowledge. Work in order, check actual messages, and stop at the concrete recovery action when a result differs.

> **Fewer screenshots, same proof:** The ten existing image boxes represent ten outcomes, not ten different required images. One readable image may cover several outcomes; reuse its URL in each matching box with its own accurate caption. Keep normal-size text. Existing split screenshots remain valid. Extra images are optional. Selected public inspection text supplements the CSR/certificate overviews; it does not replace the other required outcomes.

## Choose your starting point

- **Starting for the first time:** read the safety rule, environment checklist and “Before You Type Commands,” make your predictions, then go directly to **Part A, Step 1**. Do not work through recovery first.
- **Continuing saved work:** go to **Continuing saved work — Returning to This Lab? Start Here**, after Step 14. Find your saved **My exact practice path**, reopen it, check files, and resume at your first unfinished step. Do not rerun Step 2 just to continue.

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

Respond to the Portal's idle warning while actively working. A stopped/deallocated VM is not deleted; restart it from My Lab Environment. Saved files remain, but the TLS listener must be started again after a restart. Reopen your recorded practice path instead of regenerating keys over existing files: follow **Continuing saved work — Returning to This Lab? Start Here** after Step 14. After a browser disconnect alone, inspect the listener because it may still be running.

### Before You Type Commands

1. Work in your assigned VM, using the account named `analyst`. Do not paste these commands into your personal computer’s terminal.
2. Copy one entire **bash** box at a time, paste it into the terminal, and press Enter if the last line has not run. Do not copy the box borders or the word `bash`.
3. Copy **only commands**, never a prompt, `ACCEPT`, expected output or worksheet text. Wait for your usual command prompt to return before pasting the next box. A **prompt** is the terminal’s “ready for your next command” line. Step 10 is the exception: the service keeps running there.
   - Some boxes are several lines long and start with `if`. While they are being pasted the terminal shows `>` at the start of each line. That `>` means “I am waiting for the rest of this command.” It is normal and goes away once the last line (`fi`) arrives.
   - If `>` stays on screen and nothing happens, part of the box was not pasted. Press **Ctrl+C** once. The `>` disappears and your normal prompt returns. This cancels the currently unfinished command; **earlier complete commands from the paste may already have run**. Run the folder checkpoint, then repeat only the appropriate guarded action.
4. Long commands may wrap onto another screen line automatically. That is display wrapping, not an instruction to insert Enter. Copy the whole logical box, including `if` through `fi` and each checked command with its immediately following status line. Options beginning with `-` tell the program how to do this particular job; their explanations stay beside each box.
5. Text in **text** boxes is for your worksheet answers, not the terminal. Replace the blank prompts with your own observations.
6. If the result differs from the expected result, stop at that step and show your instructor the command and error. Do not remove checking options to make an error disappear.

A **path** is a file or folder’s address. `~` means your home folder. `cd` means “move into this folder”; `pwd` prints the folder you are currently in. A value such as `W9_RUN` is a temporary labelled value the command uses to remember a path. It lives only inside the terminal window where you created it: a new shell may not have it. If it is empty, quoted `cd "$W9_RUN"` errors and leaves your current folder unchanged. That is why you write your practice path down. Keep the commands exactly as written.

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

##### Check your account, tools, workspace and port

**What we are doing —** Check whether this VM is ready before creating a key.

**Where to work —** VM Terminal A on your assigned Cloud Heights Ubuntu VM as analyst.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
whoami
openssl version
command -v ss
date -u
mkdir -p ~/cloud-heights/week9-digital-trust
test -w ~/cloud-heights/week9-digital-trust && printf 'Workspace writable\n'
ss -ltn 'sport = :8443'
```

**Understand the command —** `whoami` prints your account. `openssl version` identifies the installed OpenSSL tool. `command -v ss` asks Bash where the `ss` program is; it does not install it. `date -u` displays the clock in UTC and does not change it. `mkdir -p` creates the Week 9 folder and any missing parents; existing folders stay. `~` is your home folder, here the same location as `$HOME`. `test -w` checks whether the folder is writable; `&&` displays “Workspace writable” only if that check passes. `printf` prints that message; `\n` makes a new line. `ss -ltn` lists listening (`-l`) TCP (`-t`) sockets with numeric addresses/ports (`-n`). The quoted `sport = :8443` filter selects the local port 8443. Read **Local Address:Port**, not the peer wildcard. This reads account/tool/clock/socket information and may create only the workspace folder; it makes no key and starts no service. Copy exactly.

**What you should see —** `analyst`, OpenSSL 3.x, an `ss` path, UTC time, “Workspace writable,” and only the `ss` heading if port 8443 is free.

**Record or capture now —** No screenshot here. Fill the existing environment and short-summary boxes below from your actual results.

**If yours looks different —** If anything is missing or a listener row appears, stop and ask your instructor. Do not install tools, change the clock or stop an unknown program.

**What you should see, in order**:

1. `analyst` — your account name.
2. An OpenSSL version beginning with `3.` — the tool is installed.
3. A file path ending in `ss` — the tool for listing waiting services is available.
4. Today’s date and time in **UTC**, a shared time standard. It can differ from your local clock by your time-zone offset.
5. `Workspace writable` — you can save files in the Week 9 folder.
6. A heading line beginning `State  Recv-Q  Send-Q  Local Address:Port`. **If that heading is the only line, port 8443 is free.** `ss` prints this heading even when nothing is listening, so the result is not completely blank. A second line containing `:8443` means something is already using the port.

```text
Account name shown: analyst
OpenSSL version shown: OpenSSL 3.0.2 15 Mar 2022
Path to the ss tool: /usr/bin/ss
UTC date and time shown: Tue Sep 29 19:51:39 UTC 2026
Workspace writable result: Workspace writable
Port 8443 check result (heading only means free; a row with :8443 means in use): No service was listed, so nothing is listening on port 8443.
```

**Stop and ask for help** if an item is missing, the date is wrong, or a service row appears. Do not close an unknown program or change the clock yourself.

**Short summary for your instructor:** copy the same four facts into this shorter box. Repeating them here is intentional; both boxes are kept so earlier saved answers stay in place.

```text
Account: analyst
OpenSSL version: OpenSSL 3.0.2 15 Mar 2022
UTC clock: Tue Sep 29 19:51:39 UTC 2026
Port 8443 free? Evidence: Yes. The ss command showed the heading but no service underneath it.
```

#### Step 2 - Create a Fresh Practice Folder

**Already recorded a practice path?** Do not run this box. Go to **Returning to This Lab? Start Here**. Running it again starts a separate new attempt (your old folder is kept).

##### Make one new attempt and record its address

**What we are doing —** Create a separate folder so earlier attempts remain untouched.

**Where to work —** VM Terminal A; first-time setup only, or a deliberately chosen fresh attempt.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
umask 077
W9_RUN=$(mktemp -d "$HOME/cloud-heights/week9-digital-trust/practice-XXXXXX")
cd "$W9_RUN"
mkdir ca requests certificates verification
printf '%s\n' "$PWD"
```

**Understand the command —** `umask 077` restricts access on files/folders subsequently created by this shell; it does not encrypt anything or change old files. `mktemp -d` creates a fresh directory. The six `X` characters are replaced by a unique ending. `$(...)` uses that command’s output as a value. `W9_RUN=...` gives that value a temporary label in this terminal; `$W9_RUN` reads it. `cd "$W9_RUN"` moves this terminal into the folder, not your files. Double quotes keep the path together. `mkdir` creates the four named subfolders here. `printf '%s\n' "$PWD"` displays the current folder so you can record it. It creates folders, not keys; output goes to the terminal. Copy the whole box exactly. The resulting path is the value you record, not a value to invent.

**What you should see —** An absolute path ending in a unique `practice-...` name.

**Record or capture now —** No screenshot here. Copy the entire printed path into **My exact practice path** immediately and Save Progress.

**If yours looks different —** If folder creation or `cd` fails, stop. Run `pwd` to see where you are; do not run Step 3 until the folder checkpoint confirms the correct attempt.

```text
My exact practice path: /home/analyst/cloud-heights/week9-digital-trust/practice-fvzj0I
```

**What you should see:** a path ending in `practice-` plus a random ending. Copy that exact path into your answer above. It will be different for each attempt.

**To resume later:** use **Returning to This Lab? Start Here**. `W9_RUN` is forgotten when this terminal closes, so your written path is what matters. Do not run Step 2 again just to resume; that would create a different attempt.

**Checkpoint before Step 3 (and after any reconnect):** run the folder checkpoint. You should see `OK: inside practice folder` and your recorded path. Right after Step 2, every file shows `missing` — that is expected.

##### Confirm the new attempt before making files

**What we are doing —** Check that setup placed you inside the expected practice folder.

**Where to work —** VM Terminal A, immediately after recording Step 2’s path.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
pwd
if [ "${PWD%/*}" = "$HOME/cloud-heights/week9-digital-trust" ] && [ -d ca ] && [ -d requests ] && [ -d certificates ] && [ -d verification ]; then printf 'OK: inside practice folder %s\n' "$PWD"; else printf 'STOP: this is not a Week 9 practice folder. Current folder: %s\n' "$PWD"; fi
for W9_FILE in requests/service.key.pem requests/service.csr.pem ca/lesson-ca.key.pem ca/lesson-ca.cert.pem ca/server-ext.cnf certificates/service.cert.pem ca/unrelated-ca.cert.pem; do if [ -e "$W9_FILE" ]; then printf 'present  %s\n' "$W9_FILE"; else printf 'missing  %s\n' "$W9_FILE"; fi; done
```

**Understand the command —** `pwd` prints your current address. `$PWD` and `$HOME` read this shell’s folder values; `${PWD%/*}` compares the parent folder without moving or renaming anything. `if/then/else/fi` chooses OK or STOP. `[ -d ... ]` checks each folder; `&&` means all checks must pass. `for/do/done` visits the supplied filenames; `[ -e ... ]` tests existence. `printf` prints labels with `%s` values and `\n` line endings. This reads names only, never private-key contents, and changes no files. Copy exactly. “present” means exists, not necessarily valid.

**What you should see —** Your recorded path and `OK: inside practice folder`. All listed files are `missing` on a brand-new attempt.

**Record or capture now —** No screenshot here. Compare the printed path to your saved answer.

**If yours looks different —** If you see STOP or a different path, stop and use the continuing-saved-work instructions.

#### Step 3 - Create the Service Key and CSR

##### Create the service’s key pair without replacing an old key

**What we are doing —** Make the service’s secret private key and a separate shareable public-key file.

**Where to work —** VM Terminal A inside your recorded practice folder.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
if [ ! -d requests ]; then
  printf 'STOP: no requests folder here. cd to your recorded practice path and run the folder checkpoint.\n'
elif [ -e requests/service.key.pem ]; then
  printf 'STOP: requests/service.key.pem already exists, so it was kept (not replaced). Run the folder checkpoint to see where to continue.\n'
  [ -e requests/service.public.pem ] || openssl pkey -in requests/service.key.pem -pubout -out requests/service.public.pem
else
  openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out requests/service.key.pem &&
  chmod 600 requests/service.key.pem &&
  openssl pkey -in requests/service.key.pem -pubout -out requests/service.public.pem &&
  printf 'Service key and public key saved\n'
fi
```

**Understand the command —** A **key pair** contains a private key kept secret and a related public key that can be shared. `if/elif/else/fi` chooses one branch. `[ ! -d requests ]` means the requests folder is absent; `!` reverses a test. `[ -e ... ]` means a file exists. `||` runs public-key extraction only if that public file does not already exist. An existing key is kept, but existence alone does not prove validity. `openssl genpkey` generates a private key; `-algorithm RSA` chooses RSA and `-pkeyopt rsa_keygen_bits:2048` chooses 2048 bits. `-out requests/service.key.pem` saves the **secret** file. `chmod 600` permits only its owner to read/write that file; it does not encrypt it. `openssl pkey -in ... -pubout -out ...` reads the private key internally and saves only its **public** part as `requests/service.public.pem`. `&&` runs the next action only after success. `printf` prints the final message. No password is added to this temporary exercise key. Copy everything exactly, including filenames; do not use an SSH login key or print the private file.

**What you should see —** Progress symbols may appear, then “Service key and public key saved,” or a clear STOP explaining an existing file.

**Record or capture now —** No screenshot here. Do not open or upload `requests/service.key.pem`.

**If yours looks different —** For an existing-file message, run the folder checkpoint and continue; do not regenerate. For any key-tool error, stop and show your instructor the error, not the key.

**What you should see:** progress symbols may appear, then `Service key and public key saved` and the prompt returns. If you see `STOP`, do not try to force a new key; follow the message. The private-key file is restricted to your account by `chmod 600`. It has no password protection for this temporary practice exercise; real systems need a planned way to protect their keys.

**Do not open or print `service.key.pem`.** `cat` displays a file’s contents, so use it only on the exact public-information files listed in these instructions.

Next make the badge application: the **CSR**. It includes the service’s public key, its requested name, and a signature made with its private key. `localhost` is the practice name for this computer. **SAN** means the list of names the certificate should cover. This request asks for `localhost`. The box also checks the request’s signature and displays the request, which contains public information.

##### Create and inspect the certificate application

**What we are doing —** Create a CSR using the service key, then check its signature and inspect its public contents.

**Where to work —** VM Terminal A inside the same recorded folder.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
if [ ! -s requests/service.key.pem ]; then
  printf 'STOP: requests/service.key.pem is missing. Run the first Step 3 box, or cd to your recorded practice path.\n'
else
  if [ -e requests/service.csr.pem ]; then
    printf 'NOTE: requests/service.csr.pem already exists, so it was kept (not remade). Checking the existing request.\n'
  else
    openssl req -new -sha256 -key requests/service.key.pem -out requests/service.csr.pem -subj "/O=CyberFoundations Lab/CN=localhost" -addext "subjectAltName=DNS:localhost"
  fi
  openssl req -in requests/service.csr.pem -noout -verify
  openssl req -in requests/service.csr.pem -noout -text > verification/csr-inspection.txt
  cat verification/csr-inspection.txt
fi
```

**Understand the command —** A **certificate signing request (CSR)** is an application containing the public key, requested identity and a signature made with the private key. **SAN** (Subject Alternative Name) is the name list; here it requests only `localhost`. `[ ! -s ... ]` stops if the key is absent or empty; `-e` keeps an existing CSR. Guards avoid replacement, not proof of validity. `openssl req -new` creates a request, `-sha256` chooses its signature digest, `-key` reads the **secret** service key internally, and `-out` saves the **public** `requests/service.csr.pem`. `-subj` supplies subject fields: `O` is organization and `CN` is common name. `-addext` adds the requested SAN. Quotes keep each supplied field together. The next `req -in ... -noout -verify` checks the CSR self-signature; `-noout` suppresses the encoded PEM request, not the check message. `-text` produces human-readable public inspection. `>` creates or replaces only `verification/csr-inspection.txt`; `cat` displays that specified **public** result. Nothing prints the private key. Copy the entire guarded block exactly; keep `localhost` and all filenames unchanged.

**What you should see —** A self-signature verification OK message and requested `DNS:localhost`. This proves the request’s signature checks, not permission to use another person’s name.

**Record or capture now —** No screenshot of the long inspection is needed now. Use the compact overview next; record actual request observations below.

**If yours looks different —** If the file is missing or the signature check fails, stop and run the folder checkpoint. Do not treat a later inspection listing as a passed signature check.

Expected: a request-signature verification message such as `Certificate request self-signature verify OK`, plus requested `DNS:localhost` in the inspection. A CSR contains public information and a signature, not the private key. Record the actual output, not this example.

**Compact EVIDENCE — CSR (after the learning inspection):** Run this read-only box for a short view of the actual subject, requested SAN, public-key algorithm/size and signature algorithm, followed by a fresh signature check. Each check's status is captured immediately. `0` means it ran successfully; a nonzero status or missing required field means stop, not “passed.” The selection reads named public fields, not just the first or last lines. If inspection fails, the complete error is shown.

##### Collect a short, public CSR overview

**What we are doing —** Show selected public request fields and a fresh signature-check result together.

**Where to work —** VM Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
printf '\n=== CSR PUBLIC FIELDS ===\n'
openssl req -in requests/service.csr.pem -noout -subject
W9_STATUS=$?
printf 'CSR subject inspection exit status: %s\n' "$W9_STATUS"
openssl req -in requests/service.csr.pem -noout -text > verification/evidence-csr-details.txt 2>&1
W9_STATUS=$?
printf 'CSR detail inspection exit status: %s\n' "$W9_STATUS"
if [ "$W9_STATUS" -eq 0 ]; then
  awk '/Public Key Algorithm:|Public-Key:|Signature Algorithm:/ {print} /X509v3 Subject Alternative Name:/ {print; getline; print}' verification/evidence-csr-details.txt
else
  cat verification/evidence-csr-details.txt
fi
printf '\n=== CSR SIGNATURE CHECK (must pass) ===\n'
openssl req -in requests/service.csr.pem -noout -verify
W9_STATUS=$?
printf 'CSR signature check exit status: %s\n' "$W9_STATUS"
```

**Understand the command —** `printf` prints labelled sections; `\n` adds line breaks and `%s` inserts the saved status. `req -in ... -noout -subject` reads the public CSR and shows its subject. `req -text` reads the same public CSR for detailed fields. `> verification/evidence-csr-details.txt 2>&1` replaces that public report and sends errors (2) to the same destination as normal output (1), so an error is not hidden. Immediately after each check, `W9_STATUS=$?` saves the last command’s exit status before another command replaces it. Zero means that command succeeded; nonzero requires reading the cause. `[ "$W9_STATUS" -eq 0 ]` tests numeric equality. `awk` selects named algorithm/size/signature lines; at the SAN heading, `getline` reads its next line and `print` shows it. This selection does not create a success or validate the name. On inspection failure, `cat` shows the complete public/error report. The final `req -verify` independently checks the signature. This reads only the public CSR, rewrites only a public report and prints to the terminal. Copy exactly, especially each immediate status capture.

**What you should see —** Actual subject, SAN and key/signature fields; inspection statuses 0 and a signature-check OK message/status 0.

**Record or capture now —** Capture the CSR overview now using the existing filename below. Selected public details can go into the existing CSR public text box if they do not fit.

**If yours looks different —** If a status is nonzero or an expected field is absent, stop and check your folder/files; do not paste example success text.

Aim for **one readable overview screenshot** with the CSR subject/SAN and signature-check message/status. The public key and signature algorithm/size may be recorded in the **CSR public inspection text** box below if the overview does not fit them. Use your actual output; do not copy sample success messages.

```text
(paste your selected CSR public inspection output here)
```

**Capture now (Step 3):** Save `week09-lab02-csr-check.png` showing the compact CSR overview and passing signature check. Put any remaining requested public details in the text box above; never capture private key contents. Extra screenshots are optional.

```text
Requested subject and SAN: O = CyberFoundations Lab, CN = localhost
SAN = DNS:localhost
CSR signature-check result: Certificate request self-signature verify OK
What the successful signature check tells me about the request: It tells me the CSR was signed using the private key that matches the public key in the request.
Why signing an application does not prove permission to use someone else’s website name: The signature only proves the matching private key was used. It does not prove the person has permission to request a certificate for someone else’s website.
```

*Quick understanding check — Why keep the private key separate from the CSR? Instructor reasoning: The CSR shares the public identity request and its signature; sharing the private key would let someone else act as that key holder. Discuss or think it through; no extra required answer or graded task.*

#### Step 4 - Model the Issuing Office

##### Create the lesson’s issuing office

**What we are doing —** Make a separate CA key and its self-signed public certificate.

**Where to work —** VM Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
if [ ! -d ca ]; then
  printf 'STOP: no ca folder here. cd to your recorded practice path and run the folder checkpoint.\n'
elif [ -e ca/lesson-ca.key.pem ] || [ -e ca/lesson-ca.cert.pem ]; then
  printf 'NOTE: the lesson CA already exists here, so it was kept (not replaced).\n'
else
  openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out ca/lesson-ca.key.pem &&
  openssl req -new -x509 -sha256 -days 365 -key ca/lesson-ca.key.pem -out ca/lesson-ca.cert.pem -subj "/O=CyberFoundations Lab/CN=Week 9 Lesson CA" -addext "basicConstraints=critical,CA:TRUE,pathlen:0" -addext "keyUsage=critical,keyCertSign,cRLSign" &&
  chmod 600 ca/lesson-ca.key.pem
fi
[ -s ca/lesson-ca.cert.pem ] && openssl x509 -in ca/lesson-ca.cert.pem -noout -subject -issuer -dates
```

**Understand the command —** A **certificate authority (CA)** is the issuing office. It must have its own key, not borrow the service’s key. `if/elif/else/fi` checks the `ca` folder and keeps both files if either already exists; `||` here means either file is enough to choose the keep branch. `genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out ...` creates the **secret** CA key with the supplied settings. In this command, `req -new -x509` creates a **self-signed CA certificate**, not a CSR: the CA signs its own public certificate. `-sha256` selects the signing digest, `-days 365` supplies its date interval, `-key` reads the secret key internally, `-out` saves the **public** certificate, and `-subj` sets its office name. `-addext` adds rules: `critical` means a checker must understand them, `CA:TRUE` allows certificate issuance, `pathlen:0` allows no subordinate CA beneath it, and `keyCertSign,cRLSign` permit certificate and revocation-list signatures. `&&` continues only on success; `chmod 600` restricts owner access without encryption. The last `[ -s ... ] && openssl x509 ...` inspects only a nonempty public certificate: `-in` reads it, `-noout` hides its encoded form, and `-subject -issuer -dates` print those fields. Copy exactly. These commands do not import any trust into your computer/browser.

**What you should see —** Subject and Issuer both name Week 9 Lesson CA, plus start/end dates.

**Record or capture now —** No screenshot here. Fill the existing CA observation box.

**If yours looks different —** If NOTE appears but no certificate fields follow, stop; a partial pair may remain. Use a fresh Step 2 attempt with a newly recorded path rather than replacing either existing file.

If you see the NOTE but no Subject/Issuer lines, only one of the two office files exists (an earlier attempt was interrupted). Do not repair it by hand: start a fresh attempt (Step 2) and record the new path.

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

A **certificate** is the signed public badge: it links the service name to its public key under the issuer’s rules. The CA’s signature can be checked mathematically; it is not a handwritten signature. A certificate contains no private key.

##### Write the final service-certificate rules

**What we are doing —** Save the seven exact settings the CA will use when issuing the certificate.

**Where to work —** VM Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
printf '%s\n' '[server_cert]' 'basicConstraints=critical,CA:FALSE' 'keyUsage=critical,digitalSignature,keyEncipherment' 'extendedKeyUsage=serverAuth' 'subjectAltName=DNS:localhost' 'subjectKeyIdentifier=hash' 'authorityKeyIdentifier=keyid,issuer' > ca/server-ext.cnf
```

**Understand the command —** `printf '%s\n'` prints each quoted argument as a complete line. Single quotes preserve the literal settings; `%s` means insert the text and `\n` means newline. `> ca/server-ext.cnf` creates or replaces this **nonsecret settings file**, not a key. `[server_cert]` names the section. `basicConstraints=critical,CA:FALSE` says this is not a CA. `keyUsage=critical,digitalSignature,keyEncipherment` supplies allowed key jobs; the TLS 1.3 exercise uses signatures, not RSA key transport. `extendedKeyUsage=serverAuth` permits server authentication. `subjectAltName=DNS:localhost` fixes the accepted name. `subjectKeyIdentifier=hash` labels the subject key; `authorityKeyIdentifier=keyid,issuer` identifies its issuer. These rules come from the issuer, not automatic acceptance of every requested extension. Copy the one complete command exactly. A screen wrap is not a new command. No existing key is changed.

**What you should see —** The ordinary prompt returns, with no printed output.

**Record or capture now —** No screenshot here. Check the file in the next action before issuing anything.

**If yours looks different —** If Bash shows an unfinished `>` prompt, press Ctrl+C once, then paste this entire command again. Cancellation stops unfinished input; earlier completed commands may already have run.

**What you should see:** the usual prompt returns; this box prints nothing.

##### Compare the complete settings before issuance

**What we are doing —** Check that the settings contain exactly the expected seven lines.

**Where to work —** VM Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
if printf '%s\n' '[server_cert]' 'basicConstraints=critical,CA:FALSE' 'keyUsage=critical,digitalSignature,keyEncipherment' 'extendedKeyUsage=serverAuth' 'subjectAltName=DNS:localhost' 'subjectKeyIdentifier=hash' 'authorityKeyIdentifier=keyid,issuer' | cmp -s - ca/server-ext.cnf; then printf 'Settings file complete: all 7 lines match\n'; else printf 'STOP: ca/server-ext.cnf is missing or incomplete. Run the settings box again.\n'; fi
cat ca/server-ext.cnf
```

**Understand the command —** `printf` produces the same literal lines as the preceding command. The pipe `|` sends that output into `cmp`. `cmp -s - ca/server-ext.cnf` compares quietly (`-s`): `-` reads the piped input and the named file is the other input. It compares complete contents, not a guessed success label. `if ... then ... else ... fi` prints complete or STOP according to that comparison. `cat ca/server-ext.cnf` displays only this **public settings file**. This changes no files; output goes to the terminal. Copy exactly, including the pipe, quotes and spacing.

**What you should see —** “Settings file complete: all 7 lines match,” then all seven settings.

**Record or capture now —** No screenshot here. Continue only after the complete message.

**If yours looks different —** If STOP appears, rerun only the preceding settings-writing command and this comparison.

**What you should see:** `Settings file complete: all 7 lines match`, then the seven lines starting with `[server_cert]`. If you see `STOP`, run the settings box again, then this check again. Do not continue until it says complete.

##### Issue and inspect the service certificate

**What we are doing —** Have the lesson CA sign the CSR using the verified final rules, then inspect the certificate.

**Where to work —** VM Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
if [ -e certificates/service.cert.pem ]; then
  printf 'NOTE: certificates/service.cert.pem already exists, so it was kept (not reissued).\n'
elif ! printf '%s\n' '[server_cert]' 'basicConstraints=critical,CA:FALSE' 'keyUsage=critical,digitalSignature,keyEncipherment' 'extendedKeyUsage=serverAuth' 'subjectAltName=DNS:localhost' 'subjectKeyIdentifier=hash' 'authorityKeyIdentifier=keyid,issuer' | cmp -s - ca/server-ext.cnf; then
  printf 'STOP: ca/server-ext.cnf is missing or incomplete. Run the settings box and the check box again.\n'
elif [ ! -s requests/service.csr.pem ] || [ ! -s ca/lesson-ca.cert.pem ] || [ ! -s ca/lesson-ca.key.pem ]; then
  printf 'STOP: a Step 3 or Step 4 file is missing. Run the folder checkpoint.\n'
else
  openssl x509 -req -in requests/service.csr.pem -CA ca/lesson-ca.cert.pem -CAkey ca/lesson-ca.key.pem -set_serial "0x$(openssl rand -hex 16)" -days 30 -sha256 -extfile ca/server-ext.cnf -extensions server_cert -out certificates/service.cert.pem
fi
[ -s certificates/service.cert.pem ] && openssl x509 -in certificates/service.cert.pem -noout -text > verification/certificate-inspection.txt && cat verification/certificate-inspection.txt
```

**Understand the command —** The guards first keep an existing certificate, reject mismatched settings (`!` reverses the `cmp` result), or stop for missing/empty required files (`-s`). `openssl x509 -req` issues a certificate from a **public CSR**: `-in` selects that CSR, `-CA` reads the **public** CA certificate, and `-CAkey` uses the **secret** CA key internally to sign. `-set_serial "0x$(openssl rand -hex 16)"` uses 16 random bytes in hexadecimal as the tracking number; `0x` marks hexadecimal and `$(...)` inserts the generated result. Yours is not an answer to copy from someone else. `-days 30` sets the certificate interval and `-sha256` chooses the signing digest. `-extfile ca/server-ext.cnf -extensions server_cert` selects our exact rule file and its section. `-out certificates/service.cert.pem` saves the **public** certificate, never over an existing one. The final nonempty-file test and `&&` inspect it only if available: `x509 -noout -text` writes readable public contents to `verification/certificate-inspection.txt`, and `cat` displays that report. `>` replaces only that report. Copy exactly; do not substitute personal names for localhost or alter filenames.

**What you should see —** Issuer Week 9 Lesson CA, subject localhost, SAN DNS:localhost, CA:FALSE and server authentication usage.

**Record or capture now —** No screenshot of the long output is needed now. Use the compact overview next and fill the existing certificate table.

**If yours looks different —** If a guard stops or a field differs, stop at this step. Run the folder checkpoint; do not delete or reissue an existing certificate.

**What you should see:** Subject includes `localhost`; Issuer includes `Week 9 Lesson CA`; SAN includes `DNS:localhost`; Basic Constraints says `CA:FALSE`; and Extended Key Usage lists TLS Web Server Authentication. If a required value differs, stop and ask for help before the next step.

**Compact EVIDENCE — issued certificate:** Keep the full inspection above for learning. This separate box prints the subject, issuer, dates, serial/fingerprint, SAN, Basic Constraints, Key Usage/EKU and named key/signature fields. It never reads private-key contents.

##### Collect a short, public certificate overview

**What we are doing —** Display final certificate identity, dates, rules and algorithm fields without printing a key.

**Where to work —** VM Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
printf '\n=== ISSUED CERTIFICATE PUBLIC FIELDS ===\n'
openssl x509 -in certificates/service.cert.pem -noout -subject -issuer -dates -serial -fingerprint -sha256 -ext subjectAltName,basicConstraints,keyUsage,extendedKeyUsage
W9_STATUS=$?
printf 'Certificate selected-field inspection exit status: %s\n' "$W9_STATUS"
openssl x509 -in certificates/service.cert.pem -noout -text > verification/evidence-cert-details.txt 2>&1
W9_STATUS=$?
printf 'Certificate key/signature inspection exit status: %s\n' "$W9_STATUS"
if [ "$W9_STATUS" -eq 0 ]; then
  awk '/Public Key Algorithm:|Public-Key:|Signature Algorithm:/ {print}' verification/evidence-cert-details.txt
else
  cat verification/evidence-cert-details.txt
fi
```

**Understand the command —** `x509 -in ...` reads the **public issued certificate**, not the CSR. `-noout` omits encoded PEM; `-subject -issuer -dates -serial` show those fields. `-fingerprint -sha256` computes a SHA-256 label for this certificate (different from the Step 6 public-key file digests). `-ext` lists the supplied SAN, constraints and usage extensions. The second `-text` inspection replaces only the **public** `verification/evidence-cert-details.txt`; `2>&1` includes errors there too. Each immediate `W9_STATUS=$?` saves that command’s real result. `if` with `-eq 0` selects `awk` named key/size/signature fields only after a successful inspection; otherwise `cat` shows the complete report. `printf` supplies headings/status labels; it cannot make an inspection succeed. Copy exactly. The only changed file is the public report and the result is displayed in your terminal.

**What you should see —** Subject/issuer/SAN, dates, rules and actual key/signature fields, with both inspection statuses 0.

**Record or capture now —** Capture the overview now using the existing issued-certificate filename. Put remaining selected public output in the existing certificate-public text box/table if needed.

**If yours looks different —** For a nonzero status or missing required field, stop and recheck the folder; do not use CSR values in place of certificate values.

**One overview plus public text is enough:** Your overview screenshot must show the issued certificate's subject/issuer and SAN. The remaining required public fields — validity dates, subject public-key algorithm/size, certificate signature algorithm, Basic Constraints, Key Usage/EKU — may be copied from this command into **Certificate public inspection text**, and explained in the existing observation table. They need not all fit in one image. Serial/fingerprint help identify your certificate; include them when shown. Do not substitute the CSR's values for issued certificate values.

```text
Subject: CN = localhost
Issuer: CN = Week 9 Lesson CA
Valid from: Oct 19 22:50:31 2026 GMT
Valid until: Nov 18 22:50:31 2026 GMT
SAN: DNS:localhost
Basic Constraints: CA:FALSE
Key Usage: Digital Signature, Key Encipherment
Extended Key Usage: TLS Web Server Authentication
Public Key Algorithm: rsaEncryption
Public-Key: 2048 bit
Signature Algorithm: sha256WithRSAEncryption
Selected-field inspection exit status: 0
Key/signature inspection exit status: 0
```

**Text safety:** These two boxes save and restore like other answers and appear as text in the GitHub worksheet. Paste only your narrowly selected public inspection output. Do not paste a private key, whole workspace archive, unreviewed session log, TLS secrets, or fabricated result. Text complements the overview images and observation table; it does not replace screenshots of fingerprint matching, offline checks, listener state or TLS checks. Blank public-text boxes are fine when your existing screenshots and table already show the details.

**Capture now (Step 5):** Save `week09-lab02-issued-certificate.png` showing the compact certificate overview. Use the public-text box and observation table for remaining details; extra images are optional.

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

##### Extract public keys and compare their fingerprints

**What we are doing —** Check whether the original public key, CSR public key and certificate public key match.

**Where to work —** VM Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
openssl req -in requests/service.csr.pem -pubkey -noout > requests/csr.public.pem
openssl x509 -in certificates/service.cert.pem -pubkey -noout > certificates/service.public.pem
sha256sum requests/service.public.pem requests/csr.public.pem certificates/service.public.pem
```

**Understand the command —** `req -in ... -pubkey -noout` reads the **public CSR** and prints only its public key; `>` saves/replaces `requests/csr.public.pem`. `x509 -in ... -pubkey -noout` similarly extracts the **public certificate key** into `certificates/service.public.pem`. `sha256sum` reads all three named **public-key files** and prints each file’s fingerprint and name. No private key is displayed or changed. Identical complete fingerprints show identical public-key file contents in this extraction format; they do not test trust, name or date. Copy exactly. These two extracted files may safely be refreshed; do not use a `.key.pem` filename as a display destination.

**What you should see —** Three complete matching digest strings, each beside the expected filename.

**Record or capture now —** Fill the existing fingerprint box. The compact checkpoint next adds extraction statuses for the screenshot.

**If yours looks different —** If an extraction errors or the digests differ, stop and check you are using one recorded attempt; a hash calculation alone is not proof of matching keys.

**What you should see:** three lines beginning with the same long string of letters and numbers. Those strings are the fingerprints, also called **digests**. Matching strings show that the same public key appears in the original public-key file, the CSR, and the certificate. They do not tell us whether the name, dates, or issuing office should be accepted. If the strings differ, stop and ask for help before continuing.

```text
Fingerprint for requests/service.public.pem: 63ce70057424616e20d47c0d4482ab23236c474c92570a7950c8564651765d0a
Fingerprint for requests/csr.public.pem: 63ce70057424616e20d47c0d4482ab23236c474c92570a7950c8564651765d0a
Fingerprint for certificates/service.public.pem: 63ce70057424616e20d47c0d4482ab23236c474c92570a7950c8564651765d0a
Do all three match, and what does that show: Yes, all three fingerprints match. This shows that the same public key was used in the original key pair, the CSR, and the finished certificate.
```

**Compact EVIDENCE — public-key correspondence:** This box refreshes only public-key files and stops if extraction fails. Compare the three full digests yourself; a successful calculation alone does not mean they match.

##### Capture the public-key comparison with real statuses

**What we are doing —** Refresh public-only extracts and show the three fingerprints only after both extractions succeed.

**Where to work —** VM Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
printf '\n=== PUBLIC-KEY CORRESPONDENCE ===\n'
openssl req -in requests/service.csr.pem -pubkey -noout > requests/csr.public.pem
W9_CSR_STATUS=$?
printf 'CSR public-key extraction exit status: %s\n' "$W9_CSR_STATUS"
openssl x509 -in certificates/service.cert.pem -pubkey -noout > certificates/service.public.pem
W9_CERT_STATUS=$?
printf 'Certificate public-key extraction exit status: %s\n' "$W9_CERT_STATUS"
if [ "$W9_CSR_STATUS" -eq 0 ] && [ "$W9_CERT_STATUS" -eq 0 ]; then
  sha256sum requests/service.public.pem requests/csr.public.pem certificates/service.public.pem
  W9_STATUS=$?
  printf 'Fingerprint calculation exit status: %s\n' "$W9_STATUS"
else
  printf 'STOP: public-key extraction failed. Do not claim matching keys. Check your folder/files.\n'
fi
```

**Understand the command —** The `req` and `x509 -pubkey -noout` commands read public input files and replace only their **public** key extracts. `W9_CSR_STATUS=$?` and `W9_CERT_STATUS=$?` immediately preserve each extraction’s result. `[ ... -eq 0 ] && [ ... -eq 0 ]` requires both to succeed before `sha256sum` reads the three files; its immediate status capture reports calculation success, not digest equality. Compare the complete strings yourself. `printf` displays section/status labels; the `else` branch says STOP on extraction failure. Copy the entire block exactly, with each status line beside its command. Secret files are neither read nor changed here.

**What you should see —** Both extraction statuses 0, three identical digest strings and fingerprint calculation status 0.

**Record or capture now —** Capture now using the existing correspondence filename below.

**If yours looks different —** If an extraction fails or a digest differs, stop and show your instructor the public result/error, not a private key.

**Capture now (Step 6):** Save `week09-lab02-key-correspondence.png` while all three matching public-key fingerprints and filenames are visible. Do not capture private key contents.

*Quick understanding check — Do matching public-key fingerprints mean the badge is trusted? Instructor reasoning: No. They compare public-key files; name, issuer, purpose and dates require certificate checks. Discuss or think it through; no extra required answer or graded task.*

#### Step 7 - Run the Passing Verification

Read the main choices in the command before running it:

| Command part | What we are asking |
|---|---|
| `-CAfile ca/lesson-ca.cert.pem` | Accept this badge office for this check. This chosen starting point is called a trust anchor. |
| `-no-CApath -no-CAstore` | Do not also use the computer’s default certificate folders or store. |
| `-purpose sslserver` | Check that the badge is suitable for a server. |
| `-verify_hostname localhost` | Check that it covers the name `localhost`. |

OpenSSL also uses the computer’s clock to check dates. The last three lines save the result number, show the message, and print that number. An **exit status** of `0` means the command succeeded; another number means it did not. Read the message to learn why. `> ... 2>&1` saves the command’s messages in a text file; it is included for you.

##### Verify the correct files and name without a connection

**What we are doing —** Check the certificate against the intended CA, purpose, name and current clock.

**Where to work —** VM Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
openssl verify -CAfile ca/lesson-ca.cert.pem -no-CApath -no-CAstore -purpose sslserver -verify_hostname localhost certificates/service.cert.pem > verification/pass.txt 2>&1
W9_STATUS=$?
printf '\n=== OFFLINE BASELINE (expected pass) ===\n'
cat verification/pass.txt
printf 'Exit status: %s\n' "$W9_STATUS"
```

**Understand the command —** `openssl verify` performs file-based validation; no listener or network connection is needed. `-CAfile ca/lesson-ca.cert.pem` explicitly accepts this **public CA certificate** as the trust anchor for this check only. `-no-CApath -no-CAstore` exclude default CA directories/store. `-purpose sslserver` checks server purpose; `-verify_hostname localhost` checks the supplied identity. The final `certificates/service.cert.pem` is the **public certificate being checked**. OpenSSL also checks validity against the VM clock. `> verification/pass.txt 2>&1` replaces this public/error result file and captures errors as well as normal output. `W9_STATUS=$?` must immediately follow verification: it saves the last command’s status before `printf` or `cat` changes `$?`. `cat` displays only that result; `printf` prints its saved status. Copy exactly; no value needs replacement.

**What you should see —** `certificates/service.cert.pem: OK` and exit status 0. This is the baseline for meaningful negative tests.

**Record or capture now —** Capture/record the existing passing outcome now, or use the grouped offline image after Step 9.

**If yours looks different —** If it fails, stop before the negative tests and check the folder and message. Do not remove verification options.

**What you should see:** `certificates/service.cert.pem: OK` and exit status `0`. This is our working starting test, also called the **baseline**. If it fails, stop and ask for help. The later wrong-input tests are meaningful only after this starting test works.

**Capture now (Step 7):** Save `week09-lab02-verify-pass.png` showing passing offline verification and exit status `0`.

```text
Command message shown by verification/pass.txt: certificates/service.cert.pem: OK
Exit status printed: 0
What this baseline result proves: The certificate passed when I used the intended CA, the expected name localhost, and the server purpose.
```

#### Step 8 - Ask for the Wrong Name on Purpose

Keep the same badge and office, but ask whether the badge covers `wrong.test`. It was issued for `localhost`, so these names should not match. Copy this complete box; the change is already made for you.

##### Negative test: ask for the wrong hostname

**What we are doing —** Check the unchanged certificate as though the expected name were wrong.test.

**Where to work —** VM Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
openssl verify -CAfile ca/lesson-ca.cert.pem -no-CApath -no-CAstore -purpose sslserver -verify_hostname wrong.test certificates/service.cert.pem > verification/wrong-name.txt 2>&1
W9_STATUS=$?
printf '\n=== OFFLINE WRONG NAME (expected rejection) ===\n'
cat verification/wrong-name.txt
printf 'Exit status: %s\n' "$W9_STATUS"
```

**Understand the command —** `openssl verify` still reads the same **public service certificate** and accepts only the lesson **public CA** via `-CAfile -no-CApath -no-CAstore`. `-purpose sslserver` still checks server use. Only `-verify_hostname wrong.test` changes: the certificate covers localhost, not this name. This offline command does not connect to wrong.test. `> verification/wrong-name.txt 2>&1` replaces only the public/error report; immediate `W9_STATUS=$?` captures verification’s result; `cat` and `printf` display it. Copy exactly, including wrong.test; do not change your actual certificate to make this pass.

**What you should see —** **This rejection is the result we want:** hostname mismatch with nonzero status, after Step 7 passed.

**Record or capture now —** Capture/record the existing wrong-name outcome now, or reuse the grouped offline image.

**If yours looks different —** For a missing-file error, stop and run the folder checkpoint. A different error is not proof of intended hostname rejection.

Expected: hostname mismatch and a nonzero exit status. The leaf and CA did not change. `wrong.test` is just an expected-name input; this offline command makes no connection to that name.

**Capture now (Step 8):** Save `week09-lab02-verify-wrong-name.png` showing the intended wrong-name rejection and nonzero status.

```text
Command message shown by verification/wrong-name.txt: error 62 at 0 depth lookup: hostname mismatch
error certificates/service.cert.pem: verification failed
Exit status printed: 2
Why the check refused this name: The certificate was made for localhost, not wrong.test. Since the name did not match, OpenSSL refused the certificate.
```

*Quick understanding check — Why is a nonzero result not enough proof of the wrong-name test? Instructor reasoning: A missing file also fails. The baseline must pass and the negative message must specifically identify a hostname mismatch. Discuss or think it through; no extra required answer or graded task.*

#### Step 9 - Ask a Different Office to Vouch for the Same Badge

##### Create a deliberately unrelated issuing office

**What we are doing —** Create a second CA that did not sign the service certificate.

**Where to work —** VM Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
if [ ! -d ca ]; then
  printf 'STOP: no ca folder here. cd to your recorded practice path and run the folder checkpoint.\n'
elif [ -e ca/unrelated-ca.key.pem ] || [ -e ca/unrelated-ca.cert.pem ]; then
  printf 'NOTE: the unrelated CA already exists here, so it was kept (not replaced). Continue with the next box.\n'
else
  openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out ca/unrelated-ca.key.pem &&
  openssl req -new -x509 -sha256 -days 365 -key ca/unrelated-ca.key.pem -out ca/unrelated-ca.cert.pem -subj "/O=CyberFoundations Lab/CN=Unrelated Lesson CA" -addext "basicConstraints=critical,CA:TRUE,pathlen:0" -addext "keyUsage=critical,keyCertSign,cRLSign" &&
  chmod 600 ca/unrelated-ca.key.pem &&
  printf 'Unrelated CA created\n'
fi
```

**Understand the command —** The `if/elif/else/fi` guard checks the CA folder and keeps either existing unrelated-office file (`-e`, with `||` meaning either). `genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out ...` creates the second **secret CA key**. `req -new -x509` creates its **public self-signed certificate**, not a CSR; `-sha256`, `-days 365`, `-key`, `-out` and `-subj` supply digest, dates, secret signing input, public output and the Unrelated Lesson CA name. `-addext` supplies critical CA:TRUE/pathlen:0 and certificate/revocation-signing permissions, as in Step 4. `&&` continues only after success; `chmod 600` restricts owner read/write but does not encrypt the key. `printf` confirms creation. Copy exactly. Neither the service certificate nor the lesson CA is replaced, and no trust store is changed.

**What you should see —** “Unrelated CA created,” or a NOTE keeping existing files.

**Record or capture now —** No screenshot of creation is needed. The next check supplies the wrong-CA evidence.

**If yours looks different —** If NOTE is followed by a missing CA file in the next check, stop; do not replace a partial key pair by hand.

##### Negative test: trust the wrong issuing office

**What we are doing —** Ask whether the unrelated CA can vouch for the unchanged service certificate.

**Where to work —** VM Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
openssl verify -CAfile ca/unrelated-ca.cert.pem -no-CApath -no-CAstore -purpose sslserver -verify_hostname localhost certificates/service.cert.pem > verification/wrong-ca.txt 2>&1
W9_STATUS=$?
printf '\n=== OFFLINE WRONG CA (expected rejection) ===\n'
cat verification/wrong-ca.txt
printf 'Exit status: %s\n' "$W9_STATUS"
```

**Understand the command —** `verify` reads the same **public service certificate**. Only `-CAfile ca/unrelated-ca.cert.pem` changes the accepted **public CA**; `-no-CApath -no-CAstore` prevent default trust from rescuing it. `-purpose sslserver` and `-verify_hostname localhost` keep purpose/name correct. `>` and `2>&1` save all result/error output in `verification/wrong-ca.txt`; immediate `W9_STATUS=$?` saves the verification result before `printf`/`cat` display it. This writes only a public/error report, not certificates or keys. Copy exactly; do not import either CA.

**What you should see —** **This rejection is the result we want:** unable to get local issuer certificate (or the matching issuer-chain failure), with nonzero status.

**Record or capture now —** Record the actual cause below. Capture it separately or use the grouped image next.

**If yours looks different —** A missing-file error is not the intended rejection. Check your recorded folder and Step 9 CA files before collecting evidence.

**What you should see:** a message such as `unable to get local issuer certificate`, and an exit status other than `0`. In plain language: “The office you gave me cannot vouch for this badge.” That refusal is expected. A missing-file error is a different problem; ask for help if you see one. Do not install these offices into your browser or computer’s accepted list.

**Compact EVIDENCE — three offline outcomes together (after Steps 7–9):** Prefer this single readable screenshot instead of three separate ones if it fits. It reruns all three checks on the same certificate and captures each real status immediately, before printing anything else. All messages remain visible — no failure is cut off. Read each message: baseline `OK`/`0`; wrong name hostname mismatch/nonzero; wrong CA issuer failure/nonzero. A missing file is NOT an intended rejection. If the baseline fails, stop and recover before collecting evidence.

##### Collect three offline outcomes in one readable view

**What we are doing —** Rerun the baseline, wrong-name and wrong-CA checks with clear separate labels.

**Where to work —** VM Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
printf '\n=== OFFLINE BASELINE (expected pass) ===\n'
openssl verify -CAfile ca/lesson-ca.cert.pem -no-CApath -no-CAstore -purpose sslserver -verify_hostname localhost certificates/service.cert.pem > verification/pass.txt 2>&1
W9_STATUS=$?
cat verification/pass.txt
printf 'Exit status: %s\n' "$W9_STATUS"
printf '\n=== OFFLINE WRONG NAME (expected rejection) ===\n'
openssl verify -CAfile ca/lesson-ca.cert.pem -no-CApath -no-CAstore -purpose sslserver -verify_hostname wrong.test certificates/service.cert.pem > verification/wrong-name.txt 2>&1
W9_STATUS=$?
cat verification/wrong-name.txt
printf 'Exit status: %s\n' "$W9_STATUS"
printf '\n=== OFFLINE WRONG CA (expected rejection) ===\n'
openssl verify -CAfile ca/unrelated-ca.cert.pem -no-CApath -no-CAstore -purpose sslserver -verify_hostname localhost certificates/service.cert.pem > verification/wrong-ca.txt 2>&1
W9_STATUS=$?
cat verification/wrong-ca.txt
printf 'Exit status: %s\n' "$W9_STATUS"
```

**Understand the command —** Each `openssl verify` reads the same **public service certificate**. `-CAfile` chooses lesson CA for baseline/name tests and unrelated CA for the issuer test; `-no-CApath -no-CAstore` keep trust isolated. `-purpose sslserver` stays fixed; `-verify_hostname` changes only for wrong.test. Each `>` replaces its matching public/error report and `2>&1` includes errors. Each immediate `W9_STATUS=$?` captures that check’s real result before `cat` and `printf` display it. The block deliberately continues to show all three outcomes; a later result does not repair an earlier failure. Labels describe expectations, not validated success. Copy exactly. No key/certificate is changed.

**What you should see —** Baseline OK/0; wrong name hostname mismatch/nonzero; wrong CA issuer failure/nonzero.

**Record or capture now —** One readable image may serve all three existing boxes, with the same URL and separate accurate captions. Existing separate captures remain valid.

**If yours looks different —** If baseline fails or a wrong test fails for another reason, stop and investigate before using this as evidence.

Save this image using `week09-lab02-verify-pass.png` and reuse its address in the passing, wrong-name and wrong-CA boxes. Captions can say “OFFLINE BASELINE: OK and status 0,” “OFFLINE WRONG NAME: hostname mismatch and nonzero status,” and “OFFLINE WRONG CA: issuer failure and nonzero status,” **only when that is your actual output**. Separate screenshots with the existing names are equally acceptable.

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

**Evidence check after Parts A–B** — cover these six outcomes, usually with four readable images (CSR overview, certificate overview, fingerprints, grouped offline checks), plus public text/table details where needed. Six separate images already taken remain acceptable:

- `week09-lab02-csr-check.png` (Step 3)
- `week09-lab02-issued-certificate.png` (Step 5)
- `week09-lab02-key-correspondence.png` (Step 6)
- `week09-lab02-verify-pass.png` (Step 7)
- `week09-lab02-verify-wrong-name.png` (Step 8)
- `week09-lab02-verify-wrong-ca.png` (Step 9)

### Part C - Try a Protected Connection on This VM

So far we checked saved files. Now we will connect two programs on the same VM.

- The **server** waits for a visitor. The **client** makes the visit and checks the badge.
- `127.0.0.1` is the address meaning “this same computer.” Connecting back to yourself is called **loopback**.
- `8443` is the numbered door, or **port**, where our server waits.
- `localhost` is the name we expect on its certificate. An address tells the client where to go; the expected name tells it which identity to check.
- **TLS 1.3** is the protected-connection version we will use. Its handshake is the initial exchange where the programs check identity and arrange message-protection keys.

You will use **two terminal sessions on the same assigned VM**:

| Terminal | Job | Its prompt |
|---|---|---|
| **Terminal A** | Runs the practice server (Step 10) and keeps running | Does **not** return while the server waits; that is normal |
| **Terminal B** | Runs every check (Steps 11 and 12) | Returns after each box finishes |

**How to open Terminal B:** keep Terminal A open. In the Lab Portal, go to **My Lab Environment** and open your **same assigned VM** again in a new browser tab. Sign in as `analyst` as usual. Then type `cd`, a space, your recorded practice path, press Enter, and run the folder checkpoint. Terminal B must show the **same path** and `present` for `certificates/service.cert.pem` and `ca/lesson-ca.cert.pem`. If you cannot open a second session, stop and ask your instructor; do not move the server elsewhere. Keep responding to idle warnings in the Portal tab.

#### Step 10 - Start the Listener in Terminal A

##### Start the waiting service, then leave Terminal A running

**What we are doing —** Start a listener only on this VM’s loopback address using your certificate/key.

**Where to work —** VM Terminal A in the recorded folder; keep this session open while using Terminal B.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
if [ ! -s certificates/service.cert.pem ] || [ ! -s requests/service.key.pem ]; then
  printf 'STOP: the certificate or key is missing here. cd to your recorded practice path and run the folder checkpoint.\n'
elif ss -ltnH 'sport = :8443' | grep -q .; then
  printf 'STOP: port 8443 is already in use. If another tab of yours is still running Step 10, use that one. Otherwise ask your instructor; do not stop other programs.\n'
else
  openssl s_server -accept 127.0.0.1:8443 -cert certificates/service.cert.pem -key requests/service.key.pem -www -tls1_3
fi
```

**Understand the command —** A **listener** is a program waiting for connections. The guards use `[ ! -s ... ]` to reject absent/empty files. `ss -ltnH` lists listening TCP numeric addresses without the heading (`-H`); the quoted `sport` filter checks local port 8443. `| grep -q .` quietly detects any row so a busy port stops startup; this checks listing output, not ownership. `openssl s_server` runs the waiting server. `-accept 127.0.0.1:8443` binds to **loopback**, meaning this same VM only, on port 8443. `-cert` reads the **public certificate** and `-key` reads its **secret key internally**; it does not print the key. `-www` provides a simple test response, not a deployed public website; do not capture its verbose response. `-tls1_3` limits this exercise to **TLS 1.3**, the protocol for checking identity and arranging protected message traffic. No files are created or changed. Copy exactly; never replace the address with a network-facing one.

**What you should see —** `ACCEPT`, with no ordinary prompt: the server is waiting, not asking you to type ACCEPT.

**Record or capture now —** Record the existing startup message/time. No screenshot yet; inspect the listener from Terminal B in Step 11.

**If yours looks different —** If port is busy, find your own earlier running Terminal A or ask your instructor. A disconnect may leave a server running: inspect rather than assuming it stopped.

**What you should see:** `ACCEPT`, and the usual prompt does not return. This is normal: the server is waiting. No client has checked its identity yet. Leave this window alone and continue in Terminal B. Keep the address exactly `127.0.0.1` so the service is available only on this VM. Do not change it to `0.0.0.0` or change network rules. Terminal A may print a few lines each time Terminal B connects; that is normal.

**After a VM stop or restart:** saved files remain but the listener must be restarted. **After only a lost browser connection:** the listener may still be running; inspect the port before assuming it stopped. Open Terminal A again, `cd` to your recorded path, run the folder checkpoint, and run **only this Step 10 box** again. Do not repeat Steps 2–9.

```text
Message shown in Terminal A after starting the listener: ACCEPT
Time you started the listener: 4:40pm EST
```

#### Step 11 - Check the Connection in Terminal B

Work in **Terminal B** (see “How to open Terminal B” above). Confirm the folder checkpoint shows your recorded path and `OK: inside practice folder` before any check. Then run:

##### Confirm the listener from the second terminal

**What we are doing —** Check the waiting service’s local address before testing its certificate.

**Where to work —** VM Terminal B, a second connection to the SAME VM and SAME recorded practice folder.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
printf '\n=== LOOPBACK LISTENER ===\n'
ss -ltn 'sport = :8443'
W9_STATUS=$?
printf 'Listener listing exit status: %s\n' "$W9_STATUS"
```

**Understand the command —** `printf` prints the section label. `ss -ltn 'sport = :8443'` lists listening TCP sockets numerically at local port 8443. Inspect the **Local Address:Port** column: a peer wildcard does not mean the listener is exposed. Immediate `W9_STATUS=$?` captures listing success. **Status 0 only means the listing worked; it does not mean a listener exists.** The heading can appear with no rows. `printf` displays the saved status. This reads socket state, changes no files and prints to Terminal B. Copy exactly.

**What you should see —** A LISTEN row with LOCAL `127.0.0.1:8443`, then Terminal B’s prompt.

**Record or capture now —** Capture the existing listener outcome now, or keep it readable with the passing TLS image below.

**If yours looks different —** If only the heading appears, return to Terminal A and inspect its startup/error message. If the LOCAL address is not loopback, stop and ask your instructor.

Expected: the heading plus one `LISTEN` row whose local address is `127.0.0.1:8443`, and Terminal B’s prompt returns. Save that evidence before the connection test.

**Capture now (Step 11):** Save `week09-lab02-loopback-listener.png` showing the `ss` row for loopback `127.0.0.1:8443` only.

```text
Listener row shown by ss: 127.0.0.1:8443
```

**Compact EVIDENCE — listener and TLS:** The `-brief` checks below show the negotiated public connection facts without verbose session secrets. Keep full errors, and do not use `-debug`, `-trace`, `-keylogfile`, or a verbose TLS dump. If readable, keep the listener row and passing TLS result together, then add the labelled wrong-name result in the same screenshot. Otherwise take one image per test; extras are optional. Reuse the shared address in the corresponding listener/TLS boxes, with captions naming each result. Never shrink text.

##### Connect and verify the correct identity

**What we are doing —** Use a checking client to connect to the waiting service and require its identity check to pass.

**Where to work —** VM Terminal B; Terminal A stays running.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
openssl s_client -connect 127.0.0.1:8443 -servername localhost -CAfile ca/lesson-ca.cert.pem -no-CApath -no-CAstore -verify_hostname localhost -verify_return_error -tls1_3 -brief < /dev/null > verification/tls-pass.txt 2>&1
W9_STATUS=$?
printf '\n=== TLS BASELINE (expected pass) ===\n'
cat verification/tls-pass.txt
printf 'Exit status: %s\n' "$W9_STATUS"
```

**Understand the command —** `openssl s_client` is the connecting checker, not the server. `-connect 127.0.0.1:8443` selects where to connect on this VM. `-servername localhost` sends the requested service name (**SNI**, Server Name Indication); it does not verify that identity. `-verify_hostname localhost` checks the actual certificate name. `-CAfile` reads the **public lesson CA**; `-no-CApath -no-CAstore` exclude default CA directories/store. `-verify_return_error` stops on certificate verification error instead of continuing anyway. `-tls1_3` uses TLS 1.3; `-brief` shows short connection facts including the cipher (message-protection method), rather than a verbose session dump. `< /dev/null` provides immediate end-of-input so the client finishes without asking for typed messages. `> verification/tls-pass.txt 2>&1` captures normal/error output in this report. Immediate `W9_STATUS=$?` saves the client result; `cat` prints only that brief report and `printf` its status. The client reads public trust; the server uses its secret key internally, and no key is printed. Copy exactly; localhost is not a personal website name to replace.

**What you should see —** TLS 1.3, `Verification: OK`, cipher information and exit status 0; Terminal B’s prompt returns.

**Record or capture now —** Capture the passing TLS outcome now; reuse the same readable image for the listener if its row is still visible.

**If yours looks different —** If connection is refused, inspect Terminal A and restart only your Step 10 listener if appropriate. For another failure, read the cause and confirm both terminals use the same folder.

**What you should see:** TLS 1.3, `Verification: OK`, and exit status `0`, then Terminal B’s prompt returns. If not, confirm Terminal A is still waiting and both terminals use the same practice folder. Ask for help if it still differs.

**Capture now (Step 11):** Save `week09-lab02-tls-pass.png` immediately after passing TLS with `Verification: OK`, TLS version/cipher and status `0`. If the listener row is still readable in this image, reuse this address in the listener box too.

Two command options have different jobs. `-servername localhost` tells the server which service name we want; this label is called **SNI**. `-verify_hostname localhost` checks whether the certificate actually covers the name. Requesting a name is not the same as checking it. `-verify_return_error` tells the client to stop if the certificate check fails. The short output also shows a **cipher**: the chosen method for protecting messages.

Now change only the expected name, keeping SNI and all other inputs the same:

##### Negative TLS test: reject the wrong hostname

**What we are doing —** Connect to the same listener but require an identity that its certificate does not cover.

**Where to work —** VM Terminal B; leave Terminal A running.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
openssl s_client -connect 127.0.0.1:8443 -servername localhost -CAfile ca/lesson-ca.cert.pem -no-CApath -no-CAstore -verify_hostname wrong.test -verify_return_error -tls1_3 -brief < /dev/null > verification/tls-wrong-name.txt 2>&1
W9_STATUS=$?
printf '\n=== TLS WRONG NAME (expected rejection) ===\n'
cat verification/tls-wrong-name.txt
printf 'Exit status: %s\n' "$W9_STATUS"
```

**Understand the command —** `s_client -connect 127.0.0.1:8443` reaches the same listener. SNI `-servername localhost` remains unchanged. Only `-verify_hostname wrong.test` changes the expected certificate identity; the service is still localhost. The same public `-CAfile`, isolated-trust options, `-verify_return_error`, TLS 1.3 and brief output remain. `< /dev/null` ends input; `> verification/tls-wrong-name.txt 2>&1` replaces only the brief public/error report. Immediate `W9_STATUS=$?` preserves the client status before `cat`/`printf` display it. Copy exactly; do not change SNI or remove verification to make the test pass.

**What you should see —** **This rejection is the result we want:** hostname mismatch/certificate name verification failure and nonzero status.

**Record or capture now —** Capture the existing TLS wrong-name outcome, or reuse one image showing both clearly labelled TLS outcomes with accurate captions.

**If yours looks different —** Connection refusal or a missing-file error is NOT intended hostname rejection. Check Terminal A and your recorded folder before rerunning.

**What you should see:** a certificate/name mismatch message and a status other than `0`. Reaching the server’s door is not enough; the badge must also pass the name check. If the message says “connection refused,” the server is not waiting, so that is not evidence of the intended name rejection.

**Capture now (Step 11):** Save `week09-lab02-tls-wrong-name.png` immediately after intended wrong-name TLS rejection with nonzero status, or reuse your TLS image if both labelled passing and wrong-name results are readable together. Identify each result in its own caption.

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

*Quick understanding check — Does SNI alone verify the service identity? Instructor reasoning: No. SNI requests a service name; verify_hostname checks the certificate name, and verify_return_error requires rejection on a verification error. Discuss or think it through; no extra required answer or graded task.*

#### Step 12 - Stop Only Your Listener

##### Stop the server you started

**What we are doing —** Stop only your own foreground listener.

**Where to work —** Return to your running VM Terminal A, not Terminal B.

**Do this —** Press Ctrl+C once in Terminal A.

**Understand the action —** Ctrl+C interrupts the currently running foreground server in this terminal. It does not delete its files or undo earlier commands. This is different from cancelling unfinished pasted syntax. Never use a broad process-kill command.

**What you should see —** Terminal A’s ordinary prompt returns.

**Record or capture now —** No screenshot at this action; switch to Terminal B for the combined stopped-listener checkpoint below.

**If yours looks different —** If you cannot identify your running Terminal A, stop and ask your instructor; do not interrupt someone else’s program.

In Terminal B, use the original passing inputs but save this stopped-listener result in its own file:

**Compact EVIDENCE — stopped listener:** This one checkpoint shows the actual listener listing (heading only), then the complete refused-connection message and real exit status. The `ss` status says whether the listing ran; inspect its rows to decide whether a listener exists.

##### Negative connection test: check after stopping your listener

**What we are doing —** Show that no program is waiting, then demonstrate connection refusal with otherwise correct inputs.

**Where to work —** VM Terminal B, after pressing Ctrl+C only in your own running Terminal A.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
printf '\n=== STOPPED LISTENER LISTING ===\n'
ss -ltn 'sport = :8443'
W9_STATUS=$?
printf 'Listener listing exit status: %s\n' "$W9_STATUS"
openssl s_client -connect 127.0.0.1:8443 -servername localhost -CAfile ca/lesson-ca.cert.pem -no-CApath -no-CAstore -verify_hostname localhost -verify_return_error -tls1_3 -brief < /dev/null > verification/no-listener.txt 2>&1
W9_STATUS=$?
printf '\n=== NO LISTENER (expected connection refusal) ===\n'
cat verification/no-listener.txt
printf 'Exit status: %s\n' "$W9_STATUS"
```

**Understand the command —** `ss -ltn` with the local `sport` filter reads listener state; immediate `W9_STATUS=$?` reports listing success only. The heading without a row means no listener is shown. `s_client` uses the original correct `-connect`, SNI `-servername localhost`, `-verify_hostname localhost`, public `-CAfile` and isolated-trust/required-verification/TLS 1.3/brief options from the passing test. `< /dev/null` ends input. `> verification/no-listener.txt 2>&1` replaces only this brief error report; the second immediate `W9_STATUS=$?` saves the connection result. `cat` displays that report and `printf` displays the saved status. It changes no key/certificate. Copy exactly; keep this failure separate from wrong-name verification.

**What you should see —** Only the listener heading (listing status normally 0), then connection refused and nonzero client status. **This rejection is the result we want:** no listener, not a bad certificate.

**Record or capture now —** Capture the existing stopped-listener outcome now with the listing and refused-connection message/status.

**If yours looks different —** If a LISTEN row remains, inspect your own Terminal A tabs. Do not stop unknown processes; ask your instructor if you cannot identify the listener.

**What you should see:** “connection refused” and a status other than `0` when no service is waiting. There is no one at the door, so the certificate check never gets that far. Keep this result separate from the earlier wrong-badge result.

**Capture now (Step 12):** Save `week09-lab02-no-listener.png` showing the stopped listener (`ss` with no listener), connection refusal and nonzero status.

```text
ss output after you pressed Ctrl+C: No service was listening on port 8443. The ss command only showed the column headings, with no listener listed.
Message shown by verification/no-listener.txt: Connection refused / connect=111
Exit status printed: 1
Why this failure is different from a badge failing its check: The connection failed because no server was listening on port 8443. In the wrong-name test, the server was running, but the certificate name didn't match. This time, the connection never started, so the certificate wasn't even checked.
```

**Pause point:** the server is now stopped. Save your worksheet and screenshots before Part D.

**Evidence check after Part C** — cover these four additional outcomes. One clearly labelled image may cover listener plus passing and wrong-name TLS; keep the stopped-listener checkpoint separate. Use more images if needed for normal-size text. There are ten outcome boxes, not a ten-distinct-image minimum:

- `week09-lab02-loopback-listener.png` (Step 11)
- `week09-lab02-tls-pass.png` (Step 11)
- `week09-lab02-tls-wrong-name.png` (Step 11)
- `week09-lab02-no-listener.png` (Step 12)

### Part D - Explain Problems and Collect Your Work

#### Step 13 - Interpret Four Conditions

**What we are doing —** Explain four different causes instead of treating every error as the same problem.

**Where to work —** Portal, using your existing results; no new terminal commands.

**Do this —** Complete one existing row at a time. For each row, explain what went wrong, what message or field you would look at, what you would correct, and which check you would repeat. **Retest** means try the check again after a correction. Use your earlier wrong-name, wrong-office, and stopped-server results to reason about rows 1, 3, and 4. Row 2 is a pretend situation to explain in writing; do not change the VM clock or certificate dates.

| Condition | Which check cannot pass? | What would you look at? | What would you fix, and why? | Which check would you repeat? |
| --- | --- | --- | --- | --- |
| `localhost` is absent from a service's SAN | Hostname check | Look at the SAN and the hostname mismatch message. | Get a certificate that includes localhost in the SAN so the name matches. | Repeat the hostname verification check. |
| Not After is in the past relative to the correct clock | Date/validity check | Look at the Not After date and the correct system time. | Renew the expired certificate and deploy the new one so the server uses a valid certificate. | Repeat the certificate verification check. |
| An unrelated CA file is supplied | Trust/issuer check |   Look for unable to get local issuer certificate and check which CA file was supplied. | Use the correct CA file that issued the certificate so the trust chain can be verified. | Repeat the CA verification check. |
| No listener exists at `127.0.0.1:8443` | Connection check | Look for Connection refused and use ss to see if anything is listening on port 8443. | Start the correct service on 127.0.0.1:8443 because the client cannot connect if nobody is listening. | Repeat the ss check, then repeat the TLS connection check. |

**Understand the action —** A SAN mismatch concerns identity; an expired date concerns time; an unrelated CA concerns issuer trust; no listener concerns reaching a running program. These are different checks, not four names for the same failure.

**What you should see —** Your recorded messages give concrete examples for three rows; the expired-date row stays hypothetical.

**Record or capture now —** Fill the existing table and explanation below. No screenshot or clock change here.

**If yours looks different —** If you cannot connect a cause to a message, reread the matching Step 8, 9 or 12 result before writing.

A badge may need replacing when its dates run out. Getting a new certificate is called **renewal**. The service must also be set up to use the new file; that is **deployment**. An office can cancel a certificate before its end date; that is **revocation**. Our file checks did not contact an online cancellation service.

In your own words, explain why getting a replacement file is not enough until the server uses it. Then explain why our passing file check does not prove that someone checked an up-to-date cancellation list.

```text
Getting a replacement certificate is not enough because the server could still be using the old certificate. The new certificate has to be deployed so the server actually uses it. After deployment, I would test the connection again to make sure the new certificate is being presented.

Our passing file check does not prove that an up-to-date revocation list was checked because the test only used the certificate and CA file we supplied. It did not contact an online revocation service. The certificate passed our checks, but that does not prove it had not been revoked somewhere else.
```

#### Step 14 - Compare the Real Website with Your Practice Service

**What we are doing —** Compare a real website’s observed identity with this isolated practice service.

**Where to work —** Portal, with your saved Lab 01 observation and Lab 02 public results.

**Do this —** Complete one existing comparison row at a time using those two observations.

**Understand the action —** Your lesson CA is accepted only because you explicitly selected it for these checks; a real website depends on the checking browser’s accepted trust anchors and chain. Neither comparison proves unobserved facts such as current revocation checks.

**What you should see —** Different names, issuers and trust choices, with evidence for what you actually observed.

**Record or capture now —** Fill the existing comparison table. No new screenshot at this step.

**If yours looks different —** If a value is missing, revisit the corresponding saved public inspection; do not invent it or inspect a private key.

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

## Continuing saved work — Returning to This Lab? Start Here

**Use this section every time you come back** — after a break, a new browser tab, a reconnect, or a VM stop. **Do not run Step 2 again to resume.** Step 2 always makes a brand-new, empty attempt folder.

Your work lives in three different places. Keep them straight:

| Place | What is kept there | Example |
|---|---|---|
| Your VM (the Cloud Heights terminal) | Keys, the CSR, certificates, and `verification/*.txt` results | `~/cloud-heights/week9-digital-trust/practice-AbC123/` |
| Your own computer | The screenshot image files you saved | `week09-lab02-tls-pass.png` in your Downloads folder |
| Your GitHub portfolio | Uploaded screenshots and your submitted worksheet | `assets/screenshots/week-09/` and `week-09/labs/lab-02-build-and-test-digital-trust.md` |

**About `W9_RUN`:** it only remembered your folder inside the terminal where you ran Step 2. In a new terminal it may be empty. If it is empty, `cd "$W9_RUN"` produces an error and leaves you in your current folder. Typing `cd W9_RUN` looks for a folder literally named `W9_RUN`. To resume, type `cd`, a space, then paste the exact path recorded in your Step 2 answer (**My exact practice path**) and press Enter. Run `pwd` to confirm the folder.

1. Find your recorded path in your saved worksheet (Step 2 answer).
2. If you want to see which practice folders exist, run this box. It only lists folder names; it changes nothing:

##### Find an existing attempt

**What we are doing —** List earlier practice folders without changing them.

**Where to work —** VM Terminal A, after finding your Step 2 saved answer.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
ls -1d ~/cloud-heights/week9-digital-trust/practice-*/ 2>/dev/null || printf 'No practice folders found yet. Start at Step 1.\n'
```

**Understand the command —** `ls` lists names; `-1` puts one per line and `-d` lists each folder itself, not its contents. `~` is your home folder; `practice-*/` matches practice folder names. `2>/dev/null` hides listing errors by sending the error stream (2) to the discard device. `||` runs the `printf` message if the listing fails; therefore “No practice folders” may also mean the listing could not be read. `printf` displays the supplied text; `\n` ends a line. This reads folder names, creates nothing, and prints to the terminal. Copy exactly; do not guess which listed attempt is yours.

**What you should see —** One or more practice-folder paths, or the supplied message.

**Record or capture now —** No screenshot here. Match a listed path to **My exact practice path**, not to the newest-looking name.

**If yours looks different —** If your recorded folder is absent, stop and ask your instructor; do not create replacement keys.

3. Pick **the folder whose name matches your recorded path**. If you see several folders, do not just pick the newest one. If none matches or you did not record your path, stop and ask your instructor which attempt to continue.
4. **What we are doing —** Move this terminal to the existing attempt. **Where to work —** VM Terminal A. **Do this —** Type `cd`, a space, paste your exact recorded absolute path, then press Enter. **Understand the command —** `cd` changes this terminal’s current folder, not where files live. Replace only the path with **My exact practice path**; do not type the words “My exact practice path.” `~` means your home folder when Bash expands it; `$HOME` reads that home address. Use the printed absolute path to avoid ambiguity. **What you should see —** Your ordinary prompt, with no error. **Record or capture now —** No screenshot; confirm using the next checkpoint. **If yours looks different —** Run `pwd`, then stop if the recorded folder cannot be opened.
5. Run this **folder checkpoint**. It prints where you are and which lab files already exist. It does not change any file:

##### Check the resumed folder and files

**What we are doing —** Confirm where this terminal is and list which lab files already exist.

**Where to work —** VM Terminal A, after moving to your exact recorded folder.

**Do this —** Copy only the commands in this complete box. Read the explanation immediately below before pressing Enter; keep dependent lines together.

```bash
pwd
if [ "${PWD%/*}" = "$HOME/cloud-heights/week9-digital-trust" ] && [ -d ca ] && [ -d requests ] && [ -d certificates ] && [ -d verification ]; then printf 'OK: inside practice folder %s\n' "$PWD"; else printf 'STOP: this is not a Week 9 practice folder. Current folder: %s\n' "$PWD"; fi
for W9_FILE in requests/service.key.pem requests/service.csr.pem ca/lesson-ca.key.pem ca/lesson-ca.cert.pem ca/server-ext.cnf certificates/service.cert.pem ca/unrelated-ca.cert.pem; do if [ -e "$W9_FILE" ]; then printf 'present  %s\n' "$W9_FILE"; else printf 'missing  %s\n' "$W9_FILE"; fi; done
```

**Understand the command —** `pwd` shows the address of the folder this terminal is in. `$PWD` reads Bash’s current-folder value; `$HOME` reads your home-folder address. `${PWD%/*}` removes the final folder name for this comparison only, not from your files. `if ... then ... else ... fi` chooses the matching message: `fi` closes the decision. `[ ... ]` is a file/value test, and its spaces matter. `-d` means “is a folder”; `&&` requires all these checks to pass. The `for ... do ... done` loop checks each supplied filename in turn; `-e` means “exists.” `printf` formats the messages, `%s` inserts a value, and `\n` ends a line. Quotes preserve each path as one value. This reads names, including secret-key filenames, but never their contents; it changes nothing. Copy exactly.

**What you should see —** Your recorded path, `OK: inside practice folder`, and a `present` or `missing` line for each file. Existence is not proof that a file is valid.

**Record or capture now —** No screenshot here. Use the continuation table below and your saved answers together.

**If yours looks different —** If you see `STOP`, move to your recorded folder again before running any creation command.

**What you should see:** your recorded path, then `OK: inside practice folder`, then one `present` or `missing` line per file. If you see `STOP`, you are in the wrong folder: repeat steps 3–5.

Use the checkpoint to choose where to continue:

| The checkpoint shows | Continue at |
|---|---|
| `requests/service.key.pem` missing | Step 3, first box |
| Service key present, `requests/service.csr.pem` missing | Step 3, second box |
| CSR present, `ca/lesson-ca.cert.pem` missing | Step 4 |
| Lesson CA present, `certificates/service.cert.pem` missing | Step 5 |
| `certificates/service.cert.pem` present, `ca/unrelated-ca.cert.pem` missing | Step 6, then continue in order |
| Everything present | The first step whose worksheet answers you have not filled in |

Step 6 refreshes only extracted public-key files and prints fingerprints. Steps 7, 8, the second box of Step 9, 11 and 12 read your certificate/CA files and replace only the corresponding result reports in `verification/`; they do not recreate keys or certificates. Repeating those checks is safe when the required listener is in the intended state. Part C after a VM stop: restart only the listener (Step 10, Terminal A).

**Starting a fresh attempt instead:** run Step 2 again. It makes a new folder and leaves the old one untouched. Record the new path in your Step 2 answer and work only in the new folder from Step 3 onward. Never delete, move, or replace key files to “fix” an attempt.

## If Something Goes Wrong

| What you see | What it means | What to do |
|---|---|---|
| `No such file or directory`, or a `STOP: no ... folder here` message | You are not in your practice folder, or a step was skipped | `cd` to your recorded path and run the folder checkpoint; continue where it points |
| `>` at the start of the line and nothing happens | The terminal is waiting for the rest of a pasted box | Press **Ctrl+C** once to cancel unfinished input; earlier complete commands may have run. Check your recorded folder, then repeat the appropriate guarded action |
| `STOP: ... already exists, so it was kept` | This attempt already has that file | Do not force a new one. Run the folder checkpoint and continue from the step it shows, or start a fresh attempt with Step 2 |
| `STOP: ca/server-ext.cnf is missing or incomplete` | The settings file is not exactly the seven expected lines | Run the Step 5 settings box, then its check box, then the issue box |
| `key values mismatch`, or Step 6 fingerprints differ | The key and certificate come from different attempts or folders | Check both terminals show the same recorded path. If they do and it still differs, stop and ask your instructor |
| `STOP: port 8443 is already in use` | Another program, possibly your own earlier Step 10, is using the port | Look for another of your tabs still running Step 10 and use it. Otherwise ask your instructor. Never stop programs you did not start |
| `connection refused` during Step 11 | Terminal A’s server is not running | Check Terminal A still shows `ACCEPT`; if not, run only Step 10 again in Terminal A |
| Step 8 hostname mismatch, Step 9 `unable to get local issuer certificate`, Step 11 wrong-name failure | The checks refused wrong information on purpose | Expected learning evidence: record the message and exit status. Do not change options to make it pass |

Never remove checking options, add your pretend office to the computer’s accepted list, delete keys, or use `sudo` to make an error go away.

## Stop & Check

- Can you explain the separate jobs of the service’s secret key, its application (CSR), the office’s secret key, and the finished badge (certificate)?
- Does the issued SAN contain the intended name?
- Do all three public-key fingerprints match, with no private key displayed?
- Did the correct-input check pass before you tried the wrong inputs?
- Did each failure occur for the predicted reason rather than because of a missing file?
- Did the service wait only at `127.0.0.1:8443`, and did you stop it afterward?

## Test

Your evidence must show: three matching public-key fingerprints; a passing file check; refusal of the wrong name; refusal of the wrong office; a passing live TLS connection; refusal of the wrong name during TLS; and a refused connection after stopping the service. For each check, include the command you ran, the message, and the exit-status number. A number other than `0` is not enough by itself: explain the message. Do not copy the terminal’s prompt into a command.

## Capture Evidence

### Save the image on your own computer

**What we are doing —** Keep a readable image of the outcome while it is visible. **Where to work —** Your own computer’s screenshot tool while viewing the VM terminal. **Do this —** Capture the labelled command/result area and save it with the existing outcome filename. **Understand the action —** This creates an image on your own computer; it does not save the portal or upload to GitHub. **What you should see —** A readable image file you can reopen. **Record or capture now —** Review text size and crop private information before upload. **If yours looks different —** Retake at normal-size text or use several images; do not shrink or invent output.

Capture each named outcome while visible, with commands and real status together when possible. One readable image per test/logical checkpoint is the goal, not a requirement to squeeze everything into one image. Use compact EVIDENCE commands; group only clearly labelled results that naturally fit. Do not screenshot private keys, credentials, access URLs or unfiltered verbose TLS session dumps. Copy relevant result text into the worksheet too.

**Upload to your own GitHub portfolio (not the VM):** For separate images keep the existing exact filenames; for a shared image use one of the matching existing filenames, not copies under every name. Save filenames on your computer and review each image for legibility and privacy. In your connected portfolio repository open `assets/screenshots/week-09/`. If missing, from the repository root choose **Add file > Create new file** named `assets/screenshots/week-09/README.md`, add a description and **Commit changes**. Inside that folder choose **Add file > Upload files**, select reviewed images and **Commit changes**. Do not change repository visibility. Open each committed image in **Raw** or image view, right-click and **Copy image address**. Add its direct `https://` address and caption in the matching **Screenshot Links and Captions** box. Do not paste a GitHub file-view webpage (`github.com/.../blob/...`), local path or repository-relative path.

**Private repository?** You never need to make it public. A copied Raw address that ends in `?token=...` is temporary and stops working. Instead type the address in this form, using your GitHub name, repository name, and branch (usually `main`): `https://github.com/YOUR-NAME/YOUR-REPO/raw/main/assets/screenshots/week-09/week09-lab02-tls-pass.png`. It opens for you and anyone you have given repository access, such as your instructor. The portal preview may say it cannot load a private image; that is fine — check the image in your own repository. Existing valid saved addresses stay valid. A relative link such as `../../assets/screenshots/week-09/week09-lab01-certificate-fields.png` is for manually edited GitHub Markdown only, not these portal boxes.

**Order:** Capture while visible; save and review; upload and commit images; add direct links and captions; finish answers; **Save Progress**; then **Submit to GitHub**. Save Progress stores portal answers. Submit to GitHub commits worksheet Markdown and references, **not image bytes**. Inspect committed Markdown and images. If links or answers change later, Save Progress and Submit to GitHub again.

Three different buttons and actions:

| Action | What it does | What it does not do |
|---|---|---|
| **Save Progress** (portal) | Stores your answers and links in the portal. Works any time, even half-finished | Does not send anything to GitHub |
| **Upload files** (GitHub) | Puts your screenshot images in `assets/screenshots/week-09/` | Does not fill in the portal boxes |
| **Submit to GitHub** (portal) | Commits this worksheet as Markdown text, including your image addresses | Does not upload image files |

Before a new Submit to GitHub or resubmit attempt runs, the portal checks the ten required **outcome boxes** for usable image addresses, not unique addresses or ten separate uploads. The same URL is accepted in multiple boxes. Public text may supply the listed CSR/certificate details alongside their overview images; no extra text field is mandatory when existing evidence already covers them. Checks do not redo or alter past submissions. Partial Save Progress always works. The two optional extra image boxes can stay blank.

## Explain

Write 5–7 sentences telling the story of what you did. Start with “First I made…”, then describe the badge application, the issuing office’s signature, your inspection, your file checks, and the live connection. Say which office you told the client to accept. Include one wrong-input test, why it failed, and what you kept the same.

```text
First I made a private key and public key for my practice service. Then I made a CSR, which was like a badge application asking for a certificate for localhost. My Week 9 Lesson CA acted as the issuing office and used its private key to sign the certificate. I inspected the certificate and confirmed that the SAN included localhost and that the public keys matched. I checked the certificate files and then made a live TLS connection to 127.0.0.1:8443, telling the client to accept the Week 9 Lesson CA. I also changed only the expected hostname from localhost to wrong.test while keeping the CA, certificate, SNI, and connection the same, and the test failed because the certificate did not match wrong.test.
```

## Required Evidence

Cover the ten outcomes below in your own GitHub portfolio under `assets/screenshots/week-09/` (not on the VM). These are the existing filenames for separate captures; **you do not need all ten distinct files**. For grouped proof, upload one image using an appropriate existing filename, then reuse its direct address in every outcome box it proves. Keep existing links and previously submitted split screenshots — no redo:

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

Additional numbered images are optional for smaller screens or larger text; do not shrink text. Use the two optional slots for extras if needed. Keep `verification/*.txt` on the VM for reference; publish only selected public inspection text in the labelled boxes, not entire logs. Do not upload any `.key.pem` file, even though it is a classroom key.

**Reuse one image in several boxes:** (1) upload and commit the readable grouped screenshot once; (2) copy its direct image address; (3) paste the **same address** in each box whose outcome is visible; (4) give each caption the specific label/message/status it documents; (5) Save Progress, then Submit to GitHub. For example, your grouped offline image can fill three outcome boxes. The alt labels below keep the original filenames for compatibility; they do not demand separate uploads.

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

![week09-lab02-no-listener.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-09/week09-lab02-no-listener.png?utm_source=chatgpt.com)

**Caption - stopped listener:** After stopping the server, I confirmed nothing was listening on port 8443. My connection test showed "Connection refused" (errno=111) with exit status 1. This proves the connection failed before TLS certificate verification could begin.

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

**What we are doing —** Put reviewed images in your own repository and submit the worksheet that references them. **Where to work —** Your own computer, GitHub and the portal, not the VM terminal. **Do this —** Follow the numbered actions below one at a time. **Understand the action —** GitHub image upload, portal Save Progress and portal Submit to GitHub are separate saves; a download or screenshot alone is not submission. **What you should see —** Committed images plus the worksheet at its unchanged path. **Record or capture now —** Keep the existing URL/caption boxes and checklist; no new evidence is required. **If yours looks different —** Reopen the committed file/image before changing a link, then save and submit updated references.

Your **portfolio repository** is your course-work folder on GitHub. A **commit** saves a set of changes there. Use the editing or upload method you practiced in Week 7; ask for a demonstration if you need one. Upload only the listed documents and reviewed images, never the whole VM folder.

1. Capture each required outcome at its named step with compact EVIDENCE commands. Group clearly labelled results where readable; keep existing names for separate images or one appropriate existing name for a shared image. Review legibility/privacy. Extra images are optional.
2. In your own GitHub portfolio open `assets/screenshots/week-09/`, choose **Add file > Upload files** for reviewed images and **Commit changes**. Create the folder first as described above if needed.
3. Open each committed image in Raw/image view; add direct image addresses and captions in matching portal boxes. Reuse one address for all outcomes it clearly proves. Add selected public CSR/certificate inspection text if needed, complete the observation table and finish answers.
4. Press **Save Progress** to store portal answers, then **Submit to GitHub** to commit worksheet Markdown and references only, not image bytes. If anything changes later, save and submit again. Complete the Week 9 Notes and Reflection worksheets in the portal the same way; use the submissions guide for Portfolio Deliverable 3.
5. Open the committed Markdown and every image on GitHub to confirm formatting and privacy.

*CyberVisionaries Institute · CyberFoundations · Tier I*
