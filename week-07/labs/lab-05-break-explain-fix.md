# Week 7 Lab 05 — Break It, Explain It, Fix It

**Student Name:** Dominique D Fleming

**Date Completed:** September 19, 2026

**Module:** 2 — Networking & Cloud Foundations | **Week:** 7  
**Submission Path:** `week-07/labs/lab-05-break-explain-fix.md`

> ## Cloud Heights Protected-Rules Safety Rule
> Four baseline rules are protected: **100** (`allow-ssh-from-bastion`), **110** (`allow-icmp-intra-vnet`), **120** (`deny-ssh-student-subnet`), and **1000** (`deny-tcp8080-student-subnet` — Inbound Deny TCP from `10.60.6.0/26` to port `8080`). **You never modify, delete, replace, or use a protected rule as a troubleshooting target.** Create or edit student rules only in priorities **200–999**. The priority **1000** fallback deny sits after your band on purpose, so a narrower Allow you create in 200–999 is evaluated first. A mistake in your student range is recoverable and is not a grading penalty when you diagnose it honestly.

> **Evidence safety:** Never include a Cloud Heights password or Bastion shareable URL. Crop browser address bars and login information before committing screenshots.

---

## Mission

Create a controlled priority failure inside your student range, diagnose it from evidence, remove the problem, and retest. The goal is method: UNDERSTAND → PREDICT → CHANGE → TEST → VERIFY, then FORECAST → EXECUTE → VERIFY → REMEDIATE → RETEST.

## What You Already Know

A network security rule is a decision about traffic. Rules are evaluated from the lowest priority number to the highest, and the first matching rule wins. Inbound and outbound traffic use separate ledgers. A configured service, a security rule, a test result, and an evidence screenshot answer different questions.

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Required setup | Lab 03 narrow Allow present; listener running |
| Safe failure | Student-created Deny only; all four protected baselines (100, 110, 120, 1000) untouched |
| Recommended temporary rule | Priority 250 Deny TCP 8080 from `10.60.6.4` |
| Time | 45–55 minutes |

- [x] I am using my assigned `cf-student-XX` VM through the CyberFoundations Lab Portal.

- [x] The VM shows **Running**.

- [x] I can identify the four protected baseline rules at priorities 100, 110, 120, and 1000.

- [x] I understand that my editable priority range is 200–999.

### Cloud Heights Idle Stop

Cloud Heights may warn you that the VM is idle. Return to the Lab Portal and choose **I'm still working** if you are active. If the VM is stopped or deallocated, it was not deleted: restart it from **My Lab Environment**. Your disk files and saved configuration remain.

## UNDERSTAND

Your working Allow is expected at priority 300 or another student value. A new matching Deny with a lower number is evaluated first and makes the later Allow unreachable for that traffic.

## Predict First — FORECAST

Predict Grid Beacon's verdict after adding a priority 250 inbound Deny for TCP 8080 from `10.60.6.4`, while leaving the working Allow in place.

```text
DENY. The priority 250 Deny rule matches Grid Beacon’s traffic first because lower priority numbers are evaluated before the existing Allow rule. Since the first matching rule wins, the traffic is denied.
```

## CHANGE / EXECUTE

### Step 1 — Create the Temporary Fault

Create a student rule named `deny-grid-beacon-8080-test`:

- priority `250`
- Inbound / Deny / TCP
- source `10.60.6.4`; source port Any
- destination your assigned VM/default; destination port `8080`
- description: intentional Week 7 troubleshooting fault

Do not edit or delete the Lab 03 Allow. Do not touch the protected priorities 100, 110, 120, or 1000 — the priority 1000 `deny-tcp8080-student-subnet` fallback stays exactly as it is throughout this lab.

### Step 2 — Capture the Broken Ledger

Capture both the priority 250 Deny and the later Allow in the same ordered rule view.

## TEST / VERIFY

Wait at least 10 seconds, select Grid Beacon, and run **Test My Rule**. Expected verdict: `DENIED`.

```text
DENIED

No answer at all from port 8080 before the timeout. Traffic from this source appears to be blocked — a network rule may be denying it, or a higher-priority Deny may be matching first.

Source: Grid Beacon (10.60.6.4) · Port TCP 8080

My prediction my accurate.
```

### Stop & Check — Diagnose Before Fixing

Confirm these healthy facts before remediation:

- VM is Running.
- Python listener is still active on 8080.
- The original Allow is still present and correctly scoped.
- The temporary Deny is evaluated first.

```text
My diagnosis is that the traffic was denied because the temporary priority 250 Deny rule matched before the priority 300 Allow rule. I verified that the VM was still running and that the Python service was still listening on port 8080, which ruled out a stopped VM or stopped service. I also confirmed that my original Allow rule was still present and correctly scoped. Since the lower-numbered Deny rule was evaluated first, it became the first matching rule and caused the traffic to be denied.
```

## REMEDIATE

Delete only the temporary `deny-grid-beacon-8080-test` rule you created. Removing the controlled fault returns the ledger to the known-good least-privilege state.

## RETEST

1. Wait at least 10 seconds and retest Grid Beacon: expected `ALLOWED`.
2. Wait at least 10 seconds and retest Other Test Source: expected `DENIED`.

```text
(record both retest results)
```

## Capture Evidence

Your sequence must show broken rules, observed denial, repaired rules, and final retest results.

![Broken rules — week07-lab05-broken-rules.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-07/week07-lab05-broken-rules.png)

![Observed denial — week07-lab05-observed-denial.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-07/week07-lab05-observed-denial.png)

![Fixed rules — week07-lab05-fixed-rules.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-07/week07-lab05-fixed-rules.png)

![Retest results — week07-lab05-retest-results.png](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-07/week07-lab05-retest-results.png)

## Explain — Incident Note

```text
Problem: Grid Beacon could no longer reach my Python service on TCP port 8080 after I added the temporary Deny rule.
Evidence: The VM was running, Python was still listening on port 8080, and my original priority 300 Allow rule was still present and correctly scoped. I also confirmed that the new priority 250 Deny rule matched the same traffic.
Healthy conditions ruled out: I ruled out the VM being stopped, the Python service being stopped, and the original Allow rule being missing or incorrectly configured.
Root cause: The priority 250 Deny rule was evaluated before the priority 300 Allow rule. Since the first matching rule wins, the Deny rule blocked Grid Beacon before the Allow rule could be reached.
Remediation: I removed the temporary priority 250 Deny rule so the original priority 300 Allow rule could control the Grid Beacon traffic again.
Retest: I retested Grid Beacon after removing the Deny rule and confirmed that TCP 8080 returned an ALLOWED result again.
Prevention: Before adding or changing a security rule, I will check the full rule order and priorities to make sure a lower-numbered rule will not override the access I intend to allow.
```

## Required Evidence

Save screenshots in `assets/screenshots/week-07/`:

- `week07-lab05-broken-rules.png`
- `week07-lab05-observed-denial.png`
- `week07-lab05-fixed-rules.png`
- `week07-lab05-retest-results.png`

Open each image at full size before submission. Confirm that no password, Bastion shareable URL, browser address bar, or unrelated private information is visible.

## Analysis Questions

**Analysis Question 1.** Why did the correct Allow stop working even though it was never edited? (Minimum 4 sentences.)

```text
The correct Allow rule stopped working because the new priority 250 Deny rule was evaluated before the priority 300 Allow rule. Security rules are checked from the lowest priority number to the highest, and the first matching rule wins. Since the Deny rule matched the same Grid Beacon TCP 8080 traffic first, the system denied the traffic and stopped checking further rules. The Allow rule was still correct and was never changed, but it never got a chance to be evaluated.
```

**Analysis Question 2.** Why is diagnosing from evaluation order better than changing rules by trial and error? (Minimum 4 sentences.)

```text
Diagnosing from the evaluation order is better because it helps me understand exactly why the traffic was allowed or denied. I can follow the rules from the lowest priority number until I find the first matching rule that made the decision. Changing rules by trial and error could create more problems or even break access that was already working. Diagnosing first lets me fix the actual root cause instead of guessing and making unnecessary changes.
```

**Analysis Question 3.** What made this failure safe and recoverable in the course environment? (Minimum 3 sentences.)

```text
This failure was safe and recoverable because I was working within a controlled course environment with protected baseline rules. The temporary Deny rule changed the test traffic without changing or removing the protected rules that kept important access available. I could diagnose the problem, remove the temporary Deny rule, and retest the original configuration. This allowed me to learn from a real failure without permanently breaking the environment or losing access.
```

## Submission Checklist

- [x] Forecast written before the change

- [x] Temporary fault stayed in priorities 200–999

- [x] Broken ledger and DENIED result captured

- [x] VM, listener, and original Allow checked before remediation

- [x] Only the temporary Deny removed

- [x] Grid Beacon retested `ALLOWED` and Other Test Source retested `DENIED`

- [x] Incident note completed

- [x] Protected priorities 100, 110, 120, and 1000 were not changed.

- [x] Every rule I created or edited used priority 200–999.

- [x] No password, Bastion URL, or browser address bar appears in my files.

- [x] This worksheet is committed to `week-07/labs/lab-05-break-explain-fix.md`.

## GitHub / Lab Portal Submission

1. Open **Week 7 → Lab 05: Break It, Explain It, Fix It** in the CyberFoundations Lab Portal.
2. Complete every worksheet field and confirm the listed evidence filenames.
3. Upload screenshots to `assets/screenshots/week-07/`.
4. Confirm your portfolio repository is connected, then choose **Submit to GitHub**.
5. Open the committed worksheet and each image on GitHub to verify formatting, legibility, and redaction.

*CyberVisionaries Institute · CyberFoundations · Tier I*
