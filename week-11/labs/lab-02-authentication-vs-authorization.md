# Lab 02 — Authentication vs. Authorization: A Valid Badge Doesn't Open Every Door

**Learner:** Dominique D Fleming  
**Attempt:** attempt 194ECC · seed chic-w11-v1 · state revision 6 · text revision 34

> Training simulation — fictional Cloud Heights accounts and resources. Your actions change only your Week 11 learning attempt. This is not a live AD domain, Microsoft Entra tenant or Azure subscription.

## Objective

Diagnose and repair Blair's missing case access without granting update rights.

## My answers

### Prediction before testing: will Blair be able to read cases? Why?

I predict Blair can sign in because the account is enabled, unlocked, and has MFA enrolled. But I don't think Blair can read Case Summaries because the only access I found is read access to Analyst Landing through Junior-Analysts.

### Identification — using the actual account

Blair Chen is identified as P02 in the HR record, and her Cloud Identity account is cl-blair (blair@cvi-training.example). This identifies which person and account I was working with before checking her sign-in and access permissions.

### Authentication — using your sign-in event

Blair's sign-in was successful using current credentials and MFA on the managed-blair device. The simulator recorded session SES-194ECC-1, confirming Blair passed authentication, but that doesn't automatically mean she has permission to access everything.

### Authorization — using your access tests

Blair could sign in, but her first access test was DENY because she didn't have permission to read Case Summaries. After I added her to Case-Readers, the read test showed ALLOW while the update test stayed DENY, proving she only received the read access approved in T201.

### Accountability — using the audit record

The audit record shows I added Blair to the Case-Readers group to give her the read-only access approved in T201. The access tests and decision note document what changed, why it was approved, and how I verified it worked.

## Evidence

- **E03 · EV-194ECC-003** — Successful sign-in + the access result before your change (captured at revision 2)  
  _What this proves:_ The sign-in record shows Blair successfully signed in using current credentials and MFA on a managed device. The before-change access test shows Blair was denied read access to Case Summaries because no matching permission was found.  
  See [evidence index](evidence/week11-evidence-index.md#ev-194ecc-003)
- **E04 · EV-194ECC-004** — Membership/access change audit + read result + update result (captured at revision 5)  
  _What this proves:_ The audit records the change to Blair's account when I added her to the Case-Readers group. The retests show Blair can now read Case Summaries but still cannot update them, which matches the read-only access approved in T201.  
  See [evidence index](evidence/week11-evidence-index.md#ev-194ecc-004)
