# Week 10 — Lab 2: Prioritize Risks and Recommend Controls

Learner: Dominique D Fleming
Case: Cloud Heights Family Clinic — Risk & Threat Investigation
Scenario date: Friday 13 March 2026, 09:00 (clinic local time)
Report generated: 2026-10-20T00:15:33.461Z
Study mode: Guided (hints available)

Completion checklist: all required work for Lab 2 is present.

## Risk ratings

| ID | Asset | Likelihood | Why | Impact | Why | Score (L x I) | Classroom band |
| --- | --- | --- | --- | --- | --- | --- | --- |
| SC-01 | Appointment scheduling account | 3 | The receptionist received repeated fake IT emails asking for their password. The emails used urgency and a suspicious sender address. The scheduling account uses password\-only sign\-in, making it a real target. I don't know whether email filtering or other protections are in place. | 2 | If someone steals the scheduling password, they could access appointment information or disrupt scheduling. This could affect patient privacy and daily operations. There is no evidence that anyone gave away their password or that an account was compromised. | 6 | High (6–9) |
| SC-02 | Patient records application | 3 | Four employees share the same login, the password is written down in a drawer, and it hasn't been changed in eight months. It also wasn't changed when employees left. This makes unauthorized access more likely, and the clinic can't tell which person used the account. | 3 | Someone using this account without permission could view or change sensitive patient records. That could seriously affect patient privacy, trust, and clinic operations. The shared login also makes it difficult to investigate who accessed the records. | 9 | High (6–9) |
| SC-03 | Staff laptops | 2 | Two of the nine staff laptops are about 90 days behind on security updates. Updates have been postponed, and nobody is assigned to make sure they get completed. This leaves known weaknesses that someone could potentially exploit, but I don't know what other protections are in place. | 3 | If a laptop is compromised, someone could potentially access sensitive patient information or disrupt the clinic's work. Depending on how far the attack spreads, recovery could take serious time and effort. There is no evidence that a laptop has actually been compromised. | 6 | High (6–9) |
| SC-04 | Public information website | 2 | The website certificate expires on March 20, and renewals are handled manually with no assigned person or reminder. Previous renewals have happened after visitors received browser warnings, so there is evidence this could happen again. | 2 | If the certificate expires, visitors could receive security warnings and may avoid using the website. This could disrupt access to clinic information and hurt patient trust. The website only contains public information, so there is no evidence that patient records would be exposed. | 4 | Medium (3–4) |
| SC-05 | Backup archive | 3 | The last successful backup was 14 days ago, and six backup jobs reported errors. The clinic only uses one external drive that's always connected, and nobody has tested restoring the files. This makes it more likely that the backups won't work when needed. | 3 | If the clinic loses its original files and can't restore them, important patient information could be lost and daily operations could stop. Recovery could take a long time or might not be possible. I don't know whether the existing backups can actually be restored. | 9 | High (6–9) |

Bands (1–2 low, 3–4 medium, 6–9 high) are a classroom teaching aid, not a compliance standard.

## Priority risks
- SC-02 — Patient records application
- SC-05 — Backup archive

**Why these:** I chose these two because they both scored 9, the highest risk score. The shared login puts sensitive patient records at risk, and the clinic can't tell who accessed them. The backup system also has repeated errors, and nobody has tested whether the files can be restored. I would address these first because one threatens patient privacy, while the other could cause serious data loss and disrupt the clinic's operations.

## Recommended controls
### SC-02
- **Control:** Replace the shared frontdesk login with separate accounts and passwords for each employee.
- **How it helps:** This makes it easier to track who accesses patient records and remove access when someone leaves. It also reduces the risk of multiple people using the same password.
- **Risk remaining afterwards:** Someone could still steal an employee's password or misuse their own account. Separate logins reduce the risk, but they don't eliminate it.

### SC-05
- **Control:** Have the clinic's IT contractor fix the backup errors and set up a scheduled backup to a separate, protected location.
- **How it helps:** This addresses the failed backup jobs and reduces the chance of losing everything if the original files or connected drive are damaged. A separate copy gives the clinic another way to recover its data.
- **Risk remaining afterwards:** Backups could still fail, and some recent information could be lost between backups. The clinic would still need to test restoring files to make sure the backups actually work.

## Owner briefing
Word count: 135 (guide: 100–150)

After reviewing the clinic's five security risks, I found two that need attention first: the shared patient records login and the backup system. Both scored 9, the highest risk rating.

Four employees share one login, making it difficult to track who accesses patient information. I recommend giving each employee their own account and password to improve security and accountability.

The backup system also has repeated errors, and the clinic hasn't tested whether files can be restored. I recommend having the IT contractor fix the errors and set up backups in a separate, protected location.

These changes would help protect patient privacy and reduce the chance of permanent data loss. However, passwords can still be stolen, and backups can still fail. The clinic should also test restoring files regularly to make sure its backups actually work.
