# Lab 05 — Joiner, Mover, Leaver

**Learner:** Dominique D Fleming  
**Attempt:** attempt 194ECC · seed chic-w11-v1 · state revision 31 · text revision 85

> Training simulation — fictional Cloud Heights accounts and resources. Your actions change only your Week 11 learning attempt. This is not a live AD domain, Microsoft Entra tenant or Azure subscription.

## Objective

Complete four lifecycle tickets across the two directory surfaces, plus a short Azure scope comparison.

## My answers

### T501 decision table — Add / Retain / Remove / Deny / Escalate (write 'not applicable — reason' where needed)

_Not written yet._

### T502 decision table — Add / Retain / Remove / Deny / Escalate

I removed Alex's old Response groups and added him to the Audit groups because he transferred from Response to Audit. I kept his All-Staff membership for Staff Handbook access. The final tests showed DENY for Response Queue read and ALLOW for Audit Evidence export, which matches T502's approval. No extra access was needed.

### T503 decision table — Add / Retain / Remove / Deny / Escalate

I didn't add any new access because Finley's contract ended. I retained his account records and audit logs, removed his group memberships, and disabled both accounts. His new sign-in was denied because the cloud account was disabled. No escalation was needed because T503 was completed and resolved. I couldn't test an old session because none was available.

### T504 decision table — Add / Retain / Remove / Deny / Escalate

_Not written yet._

### Why is an Azure resource role/scope different from a cloud directory role?

_Not written yet._

## Evidence

- **E13 · EV-194ECC-005** — Alex before/after access and changes (captured at revision 9)  
  _What this proves:_ At this point in Alex’s move, the tests show he can sign in and read the Response Queue, but he cannot export Audit Evidence. The access tests confirm his Response-Operators membership allows reading the queue, while no matching permission allows exporting audit evidence.  
  See [evidence index](evidence/week11-evidence-index.md#ev-194ecc-005)
- **E13 · EV-194ECC-006** — Alex before/after access and changes (captured at revision 18)  
  _What this proves:_ At this point in Alex's move, the account snapshots and tests show his old Response access was removed and his new Audit access works. The audit records show his department was updated in HR and both accounts, his AD account was moved to the Audit OU, his group memberships were changed, and T502 was resolved.  
  See [evidence index](evidence/week11-evidence-index.md#ev-194ecc-006)
- **E14 · EV-194ECC-007** — Finley session/status/revocation evidence (captured at revision 28)  
  _What this proves:_ After Finley's offboarding, both accounts show disabled, and the audit records show his access was removed and cloud sessions were revoked. His new sign-in failed because the account was disabled, but I couldn't run the old-session access test because no session was available.  
  See [evidence index](evidence/week11-evidence-index.md#ev-194ecc-007)
- **E14 · EV-194ECC-008** — Finley session/status/revocation evidence (captured at revision 28)  
  _What this proves:_ After Finley's offboarding, both accounts show disabled, and the audit records show his access was removed and cloud sessions were revoked. His new sign-in failed because the account was disabled, but I couldn't run the old-session access test because no session was available.  
  See [evidence index](evidence/week11-evidence-index.md#ev-194ecc-008)
