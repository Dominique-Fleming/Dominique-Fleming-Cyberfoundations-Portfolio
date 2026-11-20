# Week 11 Reflection — IAM & Active Directory

**Student Name:** Dominique D Fleming

**Date:** 11/19/2026

Answer in your own words. A few sentences each is enough. You do not need to redo your lab answers.

1. What IAM concept became clearer this week?

```text
I understand better now how groups, roles, and permissions work together. Just because someone has an account and can log in doesn't mean they should have access to everything. Their access should depend on what they actually need for their job.
```

2. What is the difference between authentication and authorization, in your own words?

```text
Authentication is proving you're really you, like signing in with your password and MFA. Authorization is what you're allowed to do after logging in. Basically, one gets you through the door, and the other decides which rooms you can enter.
```

3. Where did least privilege affect one of your decisions?

```text
When I worked on Blair's access, Blair only needed to read Case Summaries, not make changes. So I gave Blair the access needed to read them and checked that updating them was still denied. There was no reason to give more access than necessary.
```

4. What did you learn from a joiner, mover or leaver scenario?

```text
Moving Alex from Response to Audit showed me that changing someone's job means their access needs to change too. I had to remove what Alex no longer needed and add what was needed for the new role. I also learned from Finley's offboarding that disabling an account is only part of the process. You still need to check group memberships and sessions.
```

5. What made an identity event suspicious versus merely unusual?

```text
For me, it was about looking at the whole situation instead of just one thing. Casey having several failed logins followed by a successful one caught my attention, especially with an unknown device. Alex logging in from two different locations was unusual too, but there could've been a normal explanation, like using a VPN. Neither one automatically meant the account was hacked.
```

6. What evidence helped support a conclusion you reached?

```text
Casey's sign-in records showed five failed attempts followed by a successful login. I also found that the successful login (S009) and a group change (A002) had the same correlation ID, C009. That helped me connect the records, but it didn't prove who made the change or why it happened.
```

7. What uncertainty remained, even after your investigation?

```text
I still couldn't confirm who was actually behind Casey's login attempts or why Alex's logins showed different locations. Even with the records I reviewed, there wasn't enough proof to say either account was hacked. I learned that sometimes you can find something concerning without having all the answers.
```

8. What would you explain differently to a nontechnical manager?

```text
I wouldn't throw a bunch of technical terms at them. I'd explain what happened, why it caught my attention, and what we still need to check. I'd also make it clear that something looking suspicious doesn't automatically mean someone broke into an account.
```

9. What do you want to carry into Week 12, when you turn your case file into an incident report?

```text
I want to keep focusing on the facts and not jump to conclusions. I want my report to be easy for someone else to understand, even if they don't know much about cybersecurity. I also want to make sure I can explain what the evidence shows, what I still don't know, and what should be checked next.
```

## Professional Growth Check

- [x] I can explain the difference between authentication and authorization.

- [x] I can describe least privilege with an example from my own work.

- [x] I can walk through a joiner, mover or leaver scenario.

- [x] I can say what made an identity event suspicious, and what evidence supported my conclusion.

- [x] I can name what is still uncertain and what I want to carry into Week 12.
