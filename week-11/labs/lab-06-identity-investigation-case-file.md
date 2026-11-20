# Lab 06 — Identity Investigation + Case File (Deliverable 4)

**Learner:** Dominique D Fleming  
**Attempt:** attempt 194ECC · seed chic-w11-v1 · state revision 31 · text revision 85

> Training simulation — fictional Cloud Heights accounts and resources. Your actions change only your Week 11 learning attempt. This is not a live AD domain, Microsoft Entra tenant or Azure subscription.

> **DRAFT — incomplete.** Missing:
> - Capture evidence E17.
> - Capture evidence E18.
> - Capture evidence E19.

## Objective

Correlate historical logs and assemble the Cloud Heights IAM Investigation Case File — Portfolio Deliverable 4.

## My answers

### Starting hypothesis before filtering

_Not written yet._

## IAM Investigation Case File (Portfolio Deliverable 4)

_This is a technical case file. Week 12 uses it as source material to produce the professional Technical Incident Report and Executive Summary and to finalize and present the portfolio._

### 1. Scope and environment

This investigation looked at login activity, user accounts, and access permissions in the Cloud Heights Identity Center simulator. I used the sign-in logs, audit logs, and directory tools to look for unusual activity and check how access was managed. I also reviewed the work I completed with Blair, Alex, and Finley. All of this was done in a training environment using simulated accounts and records, not real company systems.

### 2. Identity/directory trace and AD DS–Entra–Azure comparison

In this environment, I learned that one person can have accounts in different systems, and those accounts can have different access. For example, Alex had an Active Directory account (ad-alex) and a cloud account (cl-alex). When Alex moved from Response to Audit, I had to update the groups for both accounts so he had the right access for his new job.

My understanding is that AD DS mainly manages accounts and groups in a traditional company network, while Microsoft Entra ID handles cloud identities and sign-ins. Azure RBAC is used to control what someone can do with Azure resources. Groups and roles help decide what a person can access, while permissions determine what actions they can take, like reading or changing information.

### 3. Blair authentication-versus-authorization investigation

The sign-in and access records show that Blair could log in successfully but couldn't access the Case Summaries at first. After I added Blair to the Case-Readers group (LA-194ECC-3-1), I tested the access again. Test AT-194ECC-4-1 showed Blair could read Case Summaries, but AT-194ECC-5-1 showed Blair still couldn't update them. This helped me understand that authentication means proving who you are, while authorization decides what you're allowed to do.

### 4. Directory administration and sensitive-action verification

During directory administration, I reviewed the support tickets and approvals before making account changes. For Blair, I added the approved read-only access to Case Summaries (LA-194ECC-3-1). For Alex, I updated his groups when he moved from Response to Audit, and the access tests showed his old access was denied and his new access worked (AT-194ECC-16-1 and AT-194ECC-17-1). For Finley, I followed the approved offboarding request by disabling both accounts, removing group memberships, and revoking cloud sessions (LA-194ECC-19-1 through LA-194ECC-26-1). I used the ticket approvals to check that the requested changes were authorized.

### 5. Three job-role configurations, scopes and business rationales

The role configurations were set up to give people access based on what they needed for their jobs. The Case-Readers group allowed Blair to read Case Summaries but not change them. The Response-Operators group gave access to the Response Queue, while the Audit-Reviewers group allowed Alex to work with Audit Evidence after moving to the Audit team.

Each group had access to specific resources instead of everything in the system. This helped me understand least privilege, which means giving someone only the access they need to do their job and nothing extra. When Alex changed jobs, I removed his old Response access and added his new Audit access.

### 6. Access test matrix and temporary grant/revoke evidence

I tested access by checking what users could and couldn't do in the simulator. For Blair, AT-194ECC-4-1 showed that reading Case Summaries was allowed, while AT-194ECC-5-1 showed that updating them was denied. For Alex, the earlier tests showed Response Queue access was allowed (AT-194ECC-8-1), but Audit Evidence export was denied (AT-194ECC-9-1). After his job change, Response Queue access was denied (AT-194ECC-16-1), and Audit Evidence export was allowed (AT-194ECC-17-1). I also removed old group memberships during Alex's transfer and Finley's offboarding. I haven't confirmed a separate temporary-access grant and removal in my saved records.

### 7. ABAC current-policy and what-if findings

The current-policy test (AT-194ECC-29-1) showed that Alex was denied access to read the Internal Response Note because he didn't have a matching permission. I then ran two what-if tests. Changing his department to Response (AT-194ECC-30-1) and changing his device trust to unmanaged (AT-194ECC-31-1) both resulted in DENY for the same reason. These tests helped me understand that changing an attribute doesn't automatically give someone access. The what-if tests were only simulations and didn't change Alex's actual account or permissions.

### 8. Joiner/mover/leaver and unverified-reset decisions

For the lifecycle requests, I checked the support tickets and HR approvals before making account changes. For Alex's move from Response to Audit (T502), I updated his department and group memberships in both Active Directory and Cloud Identity. I also tested his access to make sure his old Response access was denied and his new Audit access worked (AT-194ECC-16-1 and AT-194ECC-17-1).

For Finley's offboarding (T503), I disabled both accounts, removed group memberships, and revoked cloud sessions. The simulator showed there were no active sessions to revoke, and a later sign-in attempt was blocked because the account was disabled (LS-194ECC-28-1).

I don't have a confirmed joiner request or an identity-verified password reset in my recorded work, so I can't claim those were completed. For an unverified reset request, I would hold off on resetting the password until the person's identity and the request were properly verified.

### 9. Historical identity findings (see findings below)

My historical findings are supported by the sign-in records I reviewed in the simulator.

My first finding involved Casey (S004–S009). There were five failed login attempts followed by a successful login within three minutes. All six attempts came from the same IP address and an unknown device. The successful login also didn't show an MFA challenge. This looked suspicious, but I couldn't confirm whether someone was guessing Casey's password or Casey was having trouble logging in.

My second finding involved Alex (S001, S010, and S011). His records showed successful logins from two different locations within two minutes. This caught my attention because it could involve a VPN or someone else using his session. However, the records didn't prove that his account was compromised.

I marked Casey's finding as High priority and Alex's as Medium priority. Both findings needed further investigation before making any conclusions.

#### Finding 1: Repeated Failed Sign-ins Followed by a Successful Login

- **Events:** S004, S005, S006, S007, S008, S009
- **Observation:** The records show five failed login attempts for Casey, followed by one successful login within three minutes. All six attempts came from the same IP address, an unknown device, and the same location. The successful login did not show an MFA check.
- **Hypothesis:** One possible explanation is that someone was trying different passwords until they got the right one. It could also have been Casey having trouble remembering their password. We don't know for sure just from these records.
- **Supported conclusion:** Based on the available records, I can confirm there were five failed login attempts followed by one successful login for Casey. All six attempts came from the same IP address and unknown device. This seems suspicious to me, but I can't say for sure that someone was trying to break into the account.
- **Uncertainty / missing information:** The records don't tell me who was actually trying to log in or if it was Casey. I also don't know if someone was trying to get into the account without permission or if Casey was just having trouble with their password.
- **Next action:** I would report the unusual login activity to the IT or security team and have them check with Casey to see if those login attempts were theirs. I would also ask them to look into why the successful login didn't show an MFA check.
- **Priority:** high — I chose High because there were several failed login attempts followed by a successful one from an unknown device. The successful login also didn't show an MFA check. If someone got into Casey's account without permission, they could access information they shouldn't have. I think this needs to be checked as soon as possible.

#### Finding 2: Successful Logins from Two Different Locations

- **Events:** S001, S010, S011
- **Observation:** The records show Alex had two successful logins just two minutes apart, but from different locations and IP addresses. Both logins used the same device ID and an existing session, meaning Alex didn't have to log in again with a password.
- **Hypothesis:** One possible explanation is that Alex was using a VPN or another connection that made the location look different. Another possibility is that someone else was using Alex's session. I can't tell which one happened just from these records.
- **Supported conclusion:** Based on the records, I can confirm Alex's account was used successfully from two different locations within two minutes. Both events show the same device ID and an existing session, but that doesn't prove someone else accessed the account.
- **Uncertainty / missing information:** I don't know why the location and IP address changed so quickly. The records also don't tell me whether Alex was using a VPN or if someone else had access to the session.
- **Next action:** I would report the unusual login activity and ask the IT or security team to check with Alex. I would also have them look into the IP address change to see if it was caused by a VPN or something suspicious.
- **Priority:** medium — I chose Medium because Alex's account showed activity from two different locations within two minutes, which seems unusual. However, both logins used the same device ID, so there could be a normal explanation. I think IT should check it, but I don't have enough evidence to call it an emergency.

### 10. Sign-in/audit correlation and one benign/ambiguous comparison

The selected records are linked by the accounts, timestamps, IP addresses, devices, and sign-in details I reviewed. For Casey, records S004–S009 showed five failed logins followed by a successful login from the same IP address and unknown device. For Alex, records S001, S010, and S011 showed successful logins from two different locations, with the last two happening within two minutes.

I also compared possible normal explanations for both situations. Casey could have been entering the wrong password, and Alex could have been using a VPN. Both situations were worth checking, but neither one proved that an account was hacked.

I haven't confirmed a specific match between these historical sign-in records and the audit logs, so I wouldn't claim that an audit event caused either situation. A shared correlation ID could help connect records for investigation, but it wouldn't automatically prove what happened.

**Benign/ambiguous comparison** (S004, S005, S006, S007, S008, S009, S010, S011): Both Casey's and Alex's login records caught my attention. Casey had five failed logins followed by a successful one, while Alex had successful logins from two different locations within two minutes. Casey could have been having password trouble, and Alex could have been using a VPN. I wouldn't automatically assume either account was hacked because the records don't prove who was actually logging in or why the locations were different.

**Live simulator chain (current attempt, not historical):** During my Week 11 work, I helped move Alex from the Response team to the Audit team and updated his access. Record LA-194ECC-15-1 shows I added him to the Audit group, and tests AT-194ECC-16-1 and AT-194ECC-17-1 show his old Response access was denied while his new Audit access was allowed.

### 11. Preliminary response/remediation recommendations, priority and uncertainty

The next action I recommend checking is Casey's login activity because I marked it as High priority. I would have IT or security check with Casey to see if those login attempts were actually theirs. I would also want to know why the successful login didn't show an MFA challenge. If the activity turns out to be unauthorized, the team should secure the account and investigate further.

For Alex, I marked the activity as Medium priority. I would check whether he was using a VPN or had another reason for logging in from two different locations so close together. I would also review any related audit records to see if anything else unusual happened.

I would continue checking that Alex's updated access and Finley's disabled accounts are working as intended. The biggest thing still unknown is who was actually behind the suspicious logins. The records raised concerns, but they didn't prove that either account was compromised.

### 12. Evidence index notes

This Case File references the following records from my Week 11 work:

- **S004–S009:** Casey's five failed logins followed by a successful login. Used in Sections 9, 10, and 11.
- **S001, S010, and S011:** Alex's successful logins from two different locations. Used in Sections 9, 10, and 11.
- **LA-194ECC-3-1:** Adding Blair to the Case-Readers group. Used in Sections 3 and 4.
- **AT-194ECC-4-1 and AT-194ECC-5-1:** Tests showing Blair could read Case Summaries but couldn't update them. Used in Sections 3, 5, and 6.
- **AT-194ECC-8-1, AT-194ECC-9-1, AT-194ECC-16-1, and AT-194ECC-17-1:** Alex's access tests before and after moving from Response to Audit. Used in Sections 4, 6, and 8.
- **LA-194ECC-15-1:** Adding Alex to the Audit-Reviewers group. Used in Section 4.
- **LA-194ECC-19-1 through LA-194ECC-26-1:** Account changes and checks during Finley's offboarding. Used in Sections 4 and 8.
- **LS-194ECC-28-1:** Finley's blocked sign-in after the account was disabled. Used in Section 8.
- **AT-194ECC-29-1:** Alex's current-policy access test, which was denied. Used in Section 7.
- **AT-194ECC-30-1 and AT-194ECC-31-1:** What-if tests for Alex's department and device trust. Both were denied. Used in Section 7.

These records support the account changes, access tests, and historical findings I described. I haven't included any unconfirmed audit-log matches or actions I couldn't verify.

### Explicit simulation/environment limitations

This investigation is limited by the information available in the Cloud Heights Identity Center simulator. I could review sign-in records, account changes, and access tests, but I couldn't confirm everything that happened outside those records.

For Casey, I couldn't confirm who was actually trying to log in or why the successful login didn't show an MFA challenge. For Alex, I couldn't confirm why his logins came from two different locations so close together. I also couldn't confirm a direct connection between the historical sign-in records and specific audit events.

The ABAC what-if tests were simulations, not actual changes to Alex's account. The tests showed access was denied, but they didn't prove how every ABAC condition would work if Alex had the required permission.

I also don't have confirmed records showing that I completed a joiner request, a temporary-access grant and removal, or an identity-verified password reset.

Since this was a training environment with fictional accounts and records, the results don't prove that a real security incident happened. More information would be needed before deciding whether either account was compromised.

### Earlier labs

- [Lab 01](lab-01-identity-directory.md)
- [Lab 02](lab-02-authentication-vs-authorization.md)
- [Lab 03](lab-03-build-and-manage-directory.md)
- [Lab 04](lab-04-least-privilege-challenge.md)
- [Lab 05](lab-05-joiner-mover-leaver.md)

## Evidence

_No evidence captured yet._
