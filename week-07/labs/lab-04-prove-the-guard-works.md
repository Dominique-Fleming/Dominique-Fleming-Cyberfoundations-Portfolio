# Week 7 Lab 04 — Prove the Guard Works ★ Deliverable 2

**Student Name:** Dominique D Fleming

**Date Completed:** September 16, 2026

**Module:** 2 — Networking & Cloud Foundations | **Week:** 7  
**Submission Path:** `week-07/labs/lab-04-prove-the-guard-works.md`

> ## Cloud Heights Protected-Rules Safety Rule
> Four baseline rules are protected: **100** (`allow-ssh-from-bastion`), **110** (`allow-icmp-intra-vnet`), **120** (`deny-ssh-student-subnet`), and **1000** (`deny-tcp8080-student-subnet` — Inbound Deny TCP from `10.60.6.0/26` to port `8080`). **You never modify, delete, replace, or use a protected rule as a troubleshooting target.** Create or edit student rules only in priorities **200–999**. The priority **1000** fallback deny sits after your band on purpose, so a narrower Allow you create in 200–999 is evaluated first. A mistake in your student range is recoverable and is not a grading penalty when you diagnose it honestly.

> **Evidence safety:** Never include a Cloud Heights password or Bastion shareable URL. Crop browser address bars and login information before committing screenshots.

---

## Mission

Assemble Deliverable 2 evidence by proving both halves of least privilege: the intended source is allowed and an unintended source is denied. A single successful test is not enough.

## What You Already Know

A network security rule is a decision about traffic. Rules are evaluated from the lowest priority number to the highest, and the first matching rule wins. Inbound and outbound traffic use separate ledgers. A configured service, a security rule, a test result, and an evidence screenshot answer different questions.

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Required setup | Lab 03 rule present; Python listener still running |
| Allowed source | Grid Beacon — `10.60.6.4` |
| Unintended source | Other Test Source — `10.60.6.10` |
| Deliverable | Security group configuration + verification evidence |
| Time | 30–40 minutes |

- [x] I am using my assigned `cf-student-XX` VM through the CyberFoundations Lab Portal.

- [x] The VM shows **Running**.

- [x] I can identify the four protected baseline rules at priorities 100, 110, 120, and 1000.

- [x] I understand that my editable priority range is 200–999.

### Cloud Heights Idle Stop

Cloud Heights may warn you that the VM is idle. Return to the Lab Portal and choose **I'm still working** if you are active. If the VM is stopped or deallocated, it was not deleted: restart it from **My Lab Environment**. Your disk files and saved configuration remain.

## Predict First

**Prerequisite from Lab 03 (required):** the Python listener is running on TCP 8080, and your narrow inbound Allow for `10.60.6.4` port `8080` exists in priorities **200–999**. If either is missing, finish Lab 03 first.

**Expected results:** Grid Beacon `10.60.6.4` is **ALLOWED** by your student Allow; Other Test Source `10.60.6.10` is **DENIED** by the protected priority **1000** `deny-tcp8080-student-subnet` fallback.

**DO NOT** create an additional deny rule, broaden your allow, or modify any protected rule to produce these results.

Without changing the Lab 03 rule, predict the result from each test source.

| Source | Prediction | Deciding rule/reason |
| --- | --- | --- |
| Grid Beacon `10.60.6.4` | Allow | allow-grid-beacon-8080 |
| Other Test Source `10.60.6.10` | Deny | deny-tcp8080-student-subnet |

## Guided Steps

### Step 1 — Verify the Final Configuration

Confirm the listener is running and the student Allow remains inbound TCP 8080 from exactly `10.60.6.4`.

### Step 2 — Test the Intended Source

Select **Grid Beacon (10.60.6.4)** and run **Test My Rule**. Record the verdict and compare it with your prediction.

```text
ALLOWED

The connection succeeded and your web server answered (HTTP 200\n\n[stderr]\n"}]}). A rule is allowing TCP 8080 from this source.

Source: Grid Beacon (10.60.6.4) · Port TCP 8080

My prediction was accurate.
```

### Step 3 — Test the Unintended Source

Wait at least 10 seconds. Select **Other Test Source (10.60.6.10)** and run the same fixed TCP 8080 test.

```text
DENIED

No answer at all from port 8080 before the timeout. Traffic from this source appears to be blocked — a network rule may be denying it, or a higher-priority Deny may be matching first.

Source: Other Test Source (10.60.6.10) · Port TCP 8080

My prediction was accurate. The first matched rule was "deny-tcp8080-student-subnet" because it DENY the subnet range 10.60.6.0/26.
```

Expected verdict: `DENIED`, produced by the protected priority 1000 fallback.

If either result differs from expected, **stop making changes**: capture the complete rule list in evaluation order plus the test result, and report it to your instructor in this worksheet. Do not add a deny rule, broaden the allow, or modify protected rules.

## Stop & Check

Your evidence pair should now prove:

- the intended connection is permitted;
- the unintended connection is not permitted;
- the service was listening during both tests;
- the rule source is narrow rather than Any.

## Test Summary

| Evidence question | Result |
| --- | --- |
| Is the service listening? | Yes |
| Is Grid Beacon allowed? | Yes |
| Is Other Test Source denied? | Yes |
| Which rule produces the intended Allow? | allow-grid-beacon-8080 |

## Capture Evidence

Capture the final rule plus both result cards. Screenshots must show the selected source and verdict. These images are the core evidence for Deliverable 2.

![Final rule — week07-lab04-final-rule.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-07/week07-lab04-final-rule.png)

![Grid Beacon ALLOWED result — week07-lab04-grid-beacon-allowed.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-07/week07-lab04-grid-beacon-allowed.png)

![Other Test Source DENIED result — week07-lab04-other-source-denied.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-07/week07-lab04-other-source-denied.png)

## Explain — Deliverable 2 Statement

Write a concise professional statement covering what you configured, the source/port scope, the two tests, and how the results prove least privilege.

```text
I configured an inbound Allow rule named allow-grid-beacon-8080 at priority 300 to allow TCP traffic from Grid Beacon at 10.60.6.4 to my VM at 10.60.6.36 on port 8080. I kept the rule narrow by allowing only the specific source and port needed for the task. I tested Grid Beacon and received an ALLOWED result, proving the approved source could reach the service. I then tested the Other Test Source at 10.60.6.10 and received a DENIED result. The positive and negative tests together show that the required traffic works while traffic from an unapproved source is blocked. This proves least privilege because I allowed only the access needed without opening TCP 8080 to unnecessary sources.
```

## Required Evidence

Save screenshots in `assets/screenshots/week-07/`:

- `week07-lab04-final-rule.png`
- `week07-lab04-grid-beacon-allowed.png`
- `week07-lab04-other-source-denied.png`

Open each image at full size before submission. Confirm that no password, Bastion shareable URL, browser address bar, or unrelated private information is visible.

## Analysis Questions

**Analysis Question 1.** Why are one ALLOWED result and one DENIED result stronger evidence together than either result alone? (Minimum 4 sentences.)

```text
One ALLOWED result and one DENIED result are stronger together because they prove both sides of the rule are working. The ALLOWED result proves the approved source can reach the service. The DENIED result proves an unapproved source cannot reach it. Together, they show that the rule allows what is needed while blocking unnecessary access, which proves least privilege better than either test alone.
```

**Analysis Question 2.** If the Other Test Source were ALLOWED, what would you inspect before changing anything? (Minimum 4 sentences.)

```text
If the Other Test Source were ALLOWED, I would first inspect the full security rule list before changing anything. I would check the source, destination, protocol, port, direction, and priority to see which rule matched that traffic. I would also make sure my rule was actually limited to Grid Beacon at 10.60.6.4 and not the whole subnet or Any source. Once I found which rule caused the unexpected Allow, I could make the correct change instead of guessing and possibly breaking something else.
```

**Analysis Question 3.** How does this evidence distinguish configuration from observed enforcement? (Minimum 3 sentences.)

```text
The configured rule shows what I intended the system to do, while the test results show what actually happened to the traffic. My rule shows that Grid Beacon should be allowed to TCP port 8080, but the ALLOWED result proves that access actually worked. The DENIED result from the Other Test Source also proves that the restriction was enforced, giving me evidence that the configuration worked as intended.
```

## Submission Checklist

- [x] Final rule screenshot shows narrow source and TCP 8080

- [x] Grid Beacon `ALLOWED` evidence captured

- [x] Other Test Source `DENIED` evidence captured

- [x] Deliverable 2 statement completed

- [x] `week07-lab04-final-rule.png` captured

- [x] `week07-lab04-grid-beacon-allowed.png` captured

- [x] `week07-lab04-other-source-denied.png` captured

- [x] Protected priorities 100, 110, 120, and 1000 were not changed.

- [x] Every rule I created or edited used priority 200–999.

- [x] No password, Bastion URL, or browser address bar appears in my files.

- [x] This worksheet is committed to `week-07/labs/lab-04-prove-the-guard-works.md`.

## GitHub / Lab Portal Submission

1. Open **Week 7 → Lab 04: Prove the Guard Works ★ Deliverable 2** in the CyberFoundations Lab Portal.
2. Complete every worksheet field and confirm the listed evidence filenames.
3. Upload screenshots to `assets/screenshots/week-07/`.
4. Confirm your portfolio repository is connected, then choose **Submit to GitHub**.
5. Open the committed worksheet and each image on GitHub to verify formatting, legibility, and redaction.

*CyberVisionaries Institute · CyberFoundations · Tier I*
