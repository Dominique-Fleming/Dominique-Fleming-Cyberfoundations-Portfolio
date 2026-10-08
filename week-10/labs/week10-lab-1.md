# Week 10 — Lab 1: Investigate What Needs Protection

Learner: Dominique D Fleming
Case: Cloud Heights Family Clinic — Risk & Threat Investigation
Scenario date: Friday 13 March 2026, 09:00 (clinic local time)
Report generated: 2026-10-08T03:38:01.245Z
Study mode: Guided (hints available)

Completion checklist: all required work for Lab 1 is present.

## Evidence added to my findings
- EV-REC-01 — Email received at the reception mailbox
- EV-REC-02 — Sender address comparison card
- EV-WKS-01 — Update status report (IT contractor, 13 March 2026)
- EV-WKS-03 — Desk photo — signed-in laptop
- EV-WKS-02 — IT contractor statement
- EV-WEB-01 — Website certificate details (captured 13 March 2026)
- EV-BAK-01 — Backup job history
- EV-BAK-02 — Restore testing statement
- EV-BAK-03 — Backup drive photo
- EV-REC-03 — Reception desk log note
- EV-REC-OFF-01 — Records application account list
- EV-REC-OFF-02 — Records access log extract
- EV-REC-OFF-03 — Practice manager statement — leavers
- EV-WEB-02 — Renewal process note
- EV-WEB-03 — What the public website actually holds

## My investigation notebook
My investigation showed that the clinic has a few security weaknesses.

1. Reception: Fake emails try to steal scheduling passwords.
2. Records Office: Four employees share one login, so it's hard to tell who accessed patient records.
3. Staff Workspace: Two laptops are missing updates, and one was left unlocked.
4. Website: The security certificate expires soon, and nobody is assigned to renew it.
5. Backup Room: Backups have errors, there's only one drive, and nobody has tested restoring the files.

My biggest takeaway: I need to protect important systems, fix weak spots, and never assume something bad happened without proof. If I don't know something, I mark it as unknown instead of guessing.

## Risk scenarios

| ID | Asset | Evidence | Threat / event | Vulnerability | Consequence | CIA | Unknown / question |
| --- | --- | --- | --- | --- | --- | --- | --- |
| SC-01 | Appointment scheduling account | EV\-REC\-01 \(Email received at the reception mailbox\); EV\-REC\-02 \(Sender address comparison card\); EV\-REC\-03 \(Reception desk log note\) | Someone could trick a receptionist into giving away their scheduling password through a fake IT email. | The scheduling account uses only a password, and staff keep receiving suspicious emails. | If someone gets the password, they could access scheduling information or interfere with appointments. | Confidentiality, Availability | I don't know whether anyone entered their password or whether an unauthorized person accessed the account. |
| SC-02 | Patient records application | EV\-REC\-OFF\-01 \(Records application account list\); EV\-REC\-OFF\-02 \(Records access log extract\); EV\-REC\-OFF\-03 \(Practice manager statement — leavers\) | Someone could use the shared login to access patient records without permission. | Four employees share one password. It isn't changed when employees leave, and the clinic can't tell who used it. | Private patient information could be exposed, and the clinic might not know who accessed it. | Confidentiality | I don't know if the recorded access was unauthorized or if former employees can still access the records system. |
| SC-03 | Staff laptops | EV\-WKS\-01 \(Update status report \(IT contractor, 13 March 2026\)\); EV\-WKS\-02 \(IT contractor statement\); EV\-WKS\-03 \(Desk photo — signed\-in laptop\) | Someone could take advantage of missing security updates to compromise a staff laptop. | Two laptops haven't been updated in 90 days because staff keep postponing restarts, and nobody follows up. | A compromised laptop could expose clinic information or stop staff from accessing email and patient records. | Confidentiality, Availability | I don't know if the missing updates could actually be exploited or if either laptop has been attacked. |
| SC-04 | Public information website | EV\-WEB\-01 \(Website certificate details \(captured 13 March 2026\)\); EV\-WEB\-02 \(Renewal process note\); EV\-WEB\-03 \(What the public website actually holds\) | The website certificate could expire before someone renews it. | Nobody is assigned to renew the certificate, and there is no reminder. | Visitors could see security warnings and have trouble getting the clinic's hours, address, and services. | Availability | I don't know if the certificate will actually expire before someone renews it. |
| SC-05 | Backup archive | EV\-BAK\-01 \(Backup job history\); EV\-BAK\-02 \(Restore testing statement\); EV\-BAK\-03 \(Backup drive photo\) | The clinic could lose its original files and be unable to restore them from the backup. | The clinic has only one backup drive, recent backup jobs have errors, and nobody has tested restoring the files. | The clinic could lose important information and struggle to keep working if the files can't be recovered. | Integrity, Availability | I don't know if the backups would actually work because nobody has tested restoring them. |

## Email analysis
1. The email says the account will close in 2 hours. It's trying to rush me.
2. The sender's email address doesn't match the real scheduling vendor.
3. The email asks for my username and password through a suspicious link.

**Safe response / reporting step:** I would not click the link, reply, or give out my password. I would report the email to the clinic's IT contractor using a trusted contact method. Or report it as phishing\(if the email has that drop down option\).

**Suspicious vs proven:** The email has clear signs of phishing, but that doesn't prove anyone fell for it. I would need to check if anyone entered their password or if someone accessed the account without permission.
