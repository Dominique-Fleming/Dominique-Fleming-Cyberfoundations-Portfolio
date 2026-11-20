# Week 11 evidence index

Simulator captures from attempt 194ECC. Hash detects content changes; it is not proof of live AD administration.

## EV-194ECC-001

- Slot E01 · M01 · revision 0
- Directory trace: Alex person, both accounts, OU, one AD grant path, one cloud role path
- SHA-256 2ffc4a88aa8239ce91515c8b5db0003b29a9ce6323c3dded8876b528520d83a5
- Caption: These snapshots show Alex Morgan has two separate accounts, one in AD (ad-alex) and one in Cloud Identity (cl-alex). I can see Alex's AD account is in the Response OU and gets access through Response-Team, while the cloud account gets access through Response-Operators and the Response Agent role.

## EV-194ECC-002

- Slot E02 · M01 · revision 0
- Comparison of Blair, Finley and Drew
- SHA-256 72aea691ba3b742ad5f6a0aff5a69b08cd445f28201397f914fb85c2a1d53fa6
- Caption: Comparing these accounts, I observed Blair gets access through Junior-Analysts, while Finley gets different access through Case-Readers. Drew's AD and cloud accounts are both disabled, and his HR record shows he has departed.

## EV-194ECC-003

- Slot E03 · M02 · revision 2
- Successful sign-in + the access result before your change
- SHA-256 94f8058e50adea26eb219b01efc8b31ce14ce0a3dd116fa192983fe808404066
- Caption: The sign-in record shows Blair successfully signed in using current credentials and MFA on a managed device. The before-change access test shows Blair was denied read access to Case Summaries because no matching permission was found.

## EV-194ECC-004

- Slot E04 · M02 · revision 5
- Membership/access change audit + read result + update result
- SHA-256 d484f487397f2d83c2865392da311c5e45956b92164ed7dc7af690ed874c30ca
- Caption: The audit records the change to Blair's account when I added her to the Case-Readers group. The retests show Blair can now read Case Summaries but still cannot update them, which matches the read-only access approved in T201.

## EV-194ECC-005

- Slot E13 · M05 · revision 9
- Alex before/after access and changes
- SHA-256 ce960e604b7bffc6404c7009743d1703523d00088ef9d5cf9b67229c25640c1b
- Caption: At this point in Alex’s move, the tests show he can sign in and read the Response Queue, but he cannot export Audit Evidence. The access tests confirm his Response-Operators membership allows reading the queue, while no matching permission allows exporting audit evidence.

## EV-194ECC-006

- Slot E13 · M05 · revision 18
- Alex before/after access and changes
- SHA-256 8e4c4bdb605dd465d3c6c21056556191cfe3515758d77c535196018612d2f96a
- Caption: At this point in Alex's move, the account snapshots and tests show his old Response access was removed and his new Audit access works. The audit records show his department was updated in HR and both accounts, his AD account was moved to the Audit OU, his group memberships were changed, and T502 was resolved.

## EV-194ECC-007

- Slot E14 · M05 · revision 28
- Finley session/status/revocation evidence
- SHA-256 bdcee7612c72f3b2a6be1d82c51b5591ffe3f26bd50a2e35c7220580211c7a91
- Caption: After Finley's offboarding, both accounts show disabled, and the audit records show his access was removed and cloud sessions were revoked. His new sign-in failed because the account was disabled, but I couldn't run the old-session access test because no session was available.

## EV-194ECC-008

- Slot E14 · M05 · revision 28
- Finley session/status/revocation evidence
- SHA-256 cac4d33620665a43aea41cc2a7a232cbb419514434d6b5c5026a064c2dd9fba0
- Caption: After Finley's offboarding, both accounts show disabled, and the audit records show his access was removed and cloud sessions were revoked. His new sign-in failed because the account was disabled, but I couldn't run the old-session access test because no session was available.
