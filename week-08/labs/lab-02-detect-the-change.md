# Week 8 Lab 02 — Detect the Change

**Student Name:** Dominique D Fleming

**Date Completed:** September 26, 2026

**Module:** 3 — Practical Cryptography  
**Submission Path:** `week-08/labs/lab-02-detect-the-change.md`

> ## STOP — Protect secrets
> Never paste, upload, commit, or screenshot a password, passphrase, or private key. Do not run `cat` on any private-key file. Submit only the worksheet and the named screenshots. Keep all cryptographic files on the VM.

## How to Use This Lab

- **[TERMINAL]** means type or paste the command into the Cloud Heights terminal.
- **[WORKSHEET]** means type your response in this lab worksheet.
- Run commands in the order shown. Do not type the sample output.
- If your result does not match the stated success check, stop at the Troubleshooting box. Do not improvise with `sudo`, package installation, Azure settings, or SSH server configuration.

## Mission

Create a SHA-256 fingerprint of the original report, change a copy, and prove that the changed copy has a different fingerprint.

## What You Already Know

A cryptographic hash produces a fixed-length digest. Hashing does not hide content and has no decrypt step. A mismatch proves the two inputs differ; it does not identify who made the change.

## Lab Environment / Pre-Lab Check

**[TERMINAL] Run:**

```bash
whoami
cf-week8-check
cd ~/cloud-heights/week8-cryptography
test -f evidence/incident-report.txt && echo "READY: starting file exists"
```

**Continue only if:** `whoami` prints `analyst`, all checks report `PASS`, and the final line is `READY: starting file exists`.

### If the VM Stops

Return to **My Lab Environment** in the Lab Portal and start your assigned VM. A stopped or deallocated VM is not deleted; saved disk files remain.

## Predict First

**[WORKSHEET]** If one line is added to a file, will its SHA-256 digest remain the same? Explain.

```text
If one line is added to a file, the SHA-256 digest will not remain the same. Any change to the file will create a different digest, so when I compare it to the original digest and they don't match, I know the file's integrity has changed.
```

## Guided Steps

### Step 1 — Hash the Original

**[TERMINAL] Run:**

```bash
sha256sum evidence/incident-report.txt | tee hashes/original.sha256
```

**Expected result:** A 64-character hexadecimal digest followed by the original filename.

### Step 2 — Create and Change a Copy

**[TERMINAL] Run:**

```bash
cp evidence/incident-report.txt hashes/incident-report-modified.txt
echo "Review Note: Integrity validation exercise completed." >> hashes/incident-report-modified.txt
tail -n 3 hashes/incident-report-modified.txt
```

**Expected result:** The added `Review Note` appears in the last three lines. The original file was not changed.

### Step 3 — Hash the Changed Copy

**[TERMINAL] Run:**

```bash
sha256sum hashes/incident-report-modified.txt | tee hashes/modified.sha256
```

### Step 4 — Compare the Digest Values

**[TERMINAL] Run:**

```bash
ORIGINAL_HASH=$(cut -d' ' -f1 hashes/original.sha256)
MODIFIED_HASH=$(cut -d' ' -f1 hashes/modified.sha256)
printf 'ORIGINAL: %s
MODIFIED: %s
' "$ORIGINAL_HASH" "$MODIFIED_HASH"
if [ "$ORIGINAL_HASH" = "$MODIFIED_HASH" ]; then
  echo "UNEXPECTED: hashes match — stop and troubleshoot"
else
  echo "EXPECTED: hashes differ because the file changed"
fi
```

**Required result:** Two different digest values and `EXPECTED: hashes differ because the file changed`.

**Evidence moment:** Capture the terminal now as `week08-lab02-sha256-comparison.png`.

## Stop & Check

If you see `UNEXPECTED`, do not submit.

### Troubleshooting

Run `tail -n 3 hashes/incident-report-modified.txt`. If the Review Note is missing, repeat Step 2 once, then repeat Steps 3–4. Do not edit the original file.

## Explain

**[WORKSHEET]** In 3–4 sentences, state what the mismatch proves and two things it does not prove.

```text
The mismatch proves that the file changed because the SHA-256 digest of the modified file does not match the original. It does not prove who changed the file or why the file was changed. The mismatch only gives us evidence that the file’s integrity has changed.
```

## Analysis Questions

1. What does the different SHA-256 value prove?

```text
The different SHA-256 value proves that the file has changed from the original.
```

2. Why does the mismatch not identify the person who changed the file?

```text
The mismatch does not identify the person who changed the file because SHA-256 only detects that the file changed. It does not record who made the change.
```

3. Why does hashing not protect confidentiality?

```text
Hashing does not protect confidentiality because it does not hide or encrypt the original file. Someone who has access to the file can still read its contents even if a SHA-256 hash was created for it.
```

## Required Evidence

- `assets/screenshots/week-08/week08-lab02-sha256-comparison.png`

## Submission Checklist

- [x] The original and changed copy have different digests.

- [x] The required success message appears.

- [x] The screenshot uses the exact filename.

- [x] The original evidence file was not modified.

- [x] Every worksheet response is complete.

## GitHub / Lab Portal Submission

1. In the Lab Portal, open the matching Week 8 lab.
2. Complete every **[WORKSHEET]** response.
3. Upload only the required screenshots to `assets/screenshots/week-08/`.
4. Select **Submit to GitHub**.
5. Open the committed worksheet and screenshots on GitHub. Confirm they are readable and contain no secrets.

**Never submit:** `.pem` files, files from `~/.ssh/`, passwords, passphrases, private-key contents, or a Bastion shareable URL.
