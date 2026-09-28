# Week 8 Lab 05 — Log In with a Key

**Student Name:** Dominique D Fleming

**Date Completed:** September 27, 2026

**Module:** 3 — Practical Cryptography  
**Submission Path:** `week-08/labs/lab-05-log-in-with-a-key.md`

> ## STOP — Protect secrets
> Never paste, upload, commit, or screenshot a password, passphrase, or private key. Do not run `cat` on any private-key file. Submit only the worksheet and the named screenshots. Keep all cryptographic files on the VM.

## How to Use This Lab

- **[TERMINAL]** means type or paste the command into the Cloud Heights terminal.
- **[WORKSHEET]** means type your response in this lab worksheet.
- Run commands in the order shown. Do not type the sample output.
- If your result does not match the stated success check, stop at the Troubleshooting box. Do not improvise with `sudo`, package installation, Azure settings, or SSH server configuration.

## Mission

Authorize the public key created in Lab 03 and prove that `analyst@localhost` authenticates with that key without falling back to the account password.

## What You Already Know

In Week 6, SSH used the `analyst` account password. Here, the server stores the public key in `authorized_keys`, while the private key stays in the client account. The same user, protocol, and destination remain; only the authentication method changes.

## Lab Environment / Pre-Lab Check

**Required prerequisite:** Complete Lab 03 first.

**[TERMINAL] Run:**

```bash
whoami
cf-week8-check
test -f ~/.ssh/week8_analyst_ed25519 &&   test -f ~/.ssh/week8_analyst_ed25519.pub &&   echo "READY: Lab 03 key pair exists" ||   echo "STOP: complete Lab 03 before continuing"
```

**Continue only if:** `whoami` prints `analyst`, all checks report `PASS`, and the final line begins with `READY`.

### If the VM Stops

Return to **My Lab Environment** in the Lab Portal and start your assigned VM. A stopped or deallocated VM is not deleted; saved disk files remain.

## Predict First

**[WORKSHEET]** Does the private key need to be copied into `authorized_keys`? Explain.

```text
No, the private key does not need to be copied into authorized_keys. The public key goes into authorized_keys, while the private key stays protected on the client and is used to prove that you have the matching key.
```

## Guided Steps

### Step 1 — Prepare `authorized_keys`

**[TERMINAL] Run:**

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

### Step 2 — Add the Week 8 Public Key Once

**[TERMINAL] Run:**

```bash
grep -qxF "$(cat ~/.ssh/week8_analyst_ed25519.pub)" ~/.ssh/authorized_keys   || cat ~/.ssh/week8_analyst_ed25519.pub >> ~/.ssh/authorized_keys
```

This command adds the key only if an identical line is not already present.

### Step 3 — Confirm the Entry and Safe Permissions

**[TERMINAL] Run:**

```bash
grep -c 'week8-analyst-key$' ~/.ssh/authorized_keys
stat -c '%a %n' ~/.ssh ~/.ssh/authorized_keys
```

**Required result:** The count is `1`; permissions show `700` for `.ssh` and `600` for `authorized_keys`.

**Evidence moment:** Capture this result as `week08-lab05-authorized-key-permissions.png`.

### Step 4 — Run the Public-Key-Only Proof

**[TERMINAL] Run:**

```bash
ssh   -o PreferredAuthentications=publickey   -o PasswordAuthentication=no   -i ~/.ssh/week8_analyst_ed25519   analyst@localhost   'echo "AUTH_TEST=PUBLICKEY_SUCCESS"; whoami; hostname'
```

If SSH asks whether to trust the host fingerprint, type `yes` and press Enter. If no fingerprint question appears, continue; `localhost` was already known. Do not delete `known_hosts` to force the question.

At `Enter passphrase for key`, enter the **Lab 03 key passphrase**. Do not enter the `analyst` account password.

**Required result:**

```text
AUTH_TEST=PUBLICKEY_SUCCESS
analyst
cf-student-XX
```

The final hostname will contain your assigned VM number instead of `XX`.

**Evidence moment:** Capture the three result lines as `week08-lab05-publickey-auth-success.png`.

## Stop & Check

If the command asks for the `analyst` account password, reports `Permission denied`, or does not print `AUTH_TEST=PUBLICKEY_SUCCESS`, do not submit. The options in the proof command disable account-password fallback for this one test only; they do not change the VM server.

### Troubleshooting

1. Run `grep -c 'week8-analyst-key$' ~/.ssh/authorized_keys`; required value is `1`.
2. Run `stat -c '%a %n' ~/.ssh ~/.ssh/authorized_keys`; required values are `700` and `600`.
3. Run `ssh-keygen -lf ~/.ssh/week8_analyst_ed25519.pub`; it must show the Lab 03 key.
4. If any check fails, stop and contact the instructor. Do not edit `/etc/ssh/sshd_config`, use `sudo`, disable password authentication, or delete `known_hosts`.

## Explain

**[WORKSHEET]** In 4–5 sentences, compare Week 6 password authentication with this key-based test. State what changed and what stayed the same.

```text
In Week 6, I used a password to authenticate and connect to the VM through SSH. In this Week 8 test, I used a private key to prove I had the matching key for the public key stored in authorized_keys. What changed was the method used to authenticate me. What stayed the same was that I was still using SSH to securely connect to the VM.
```

## Analysis Questions

1. What changed between the Week 6 and Week 8 SSH logins?

```text
What changed was the way I authenticated to the VM. In Week 6, I used a password, while in Week 8, I used key-based authentication with a private key that matched the public key stored in authorized_keys.
```

2. What stayed the same?

```text
What stayed the same was that I was still using SSH to securely connect to the same VM. The purpose of the connection did not change; only the authentication method changed.
```

3. Why does `authorized_keys` contain the public key rather than the private key?

```text
authorized_keys contains the public key because the public key is safe to share and is used by the server to verify the matching private key. The private key must stay protected and should never be copied to the server or shared.
```

4. Why is the forced proof stronger evidence than a normal successful SSH login?

```text
The forced proof is stronger evidence because it makes SSH use the private key for authentication instead of allowing another authentication method. A normal successful SSH login only proves that I connected successfully, but it may not prove which authentication method was actually used.
```

5. How is the key passphrase different from the `analyst` account password?

```text
The key passphrase protects the private key, while the analyst account password protects access to the analyst account. They have different purposes and should be kept separate and protected.
```

## Required Evidence

- `assets/screenshots/week-08/week08-lab05-authorized-key-permissions.png`
- `assets/screenshots/week-08/week08-lab05-publickey-auth-success.png`

## Submission Checklist

- [x] Lab 03 was completed first.

- [x] The authorized-key count is exactly `1`.

- [x] Permissions are `700` and `600`.

- [x] The public-key-only proof printed all three required lines.

- [x] Both screenshots use the exact filenames.

- [x] No password, passphrase, private key, or Bastion URL appears.

- [x] Every worksheet response is complete.

## GitHub / Lab Portal Submission

1. In the Lab Portal, open the matching Week 8 lab.
2. Complete every **[WORKSHEET]** response.
3. Upload only the required screenshots to `assets/screenshots/week-08/`.
4. Select **Submit to GitHub**.
5. Open the committed worksheet and screenshots on GitHub. Confirm they are readable and contain no secrets.

**Never submit:** `.pem` files, files from `~/.ssh/`, passwords, passphrases, private-key contents, or a Bastion shareable URL.
