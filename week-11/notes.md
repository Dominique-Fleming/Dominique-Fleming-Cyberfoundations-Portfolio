# Week 11 Notes — IAM & Active Directory

**Student Name:** Dominique D Fleming

**Date:** 11/19/2026

Use your own words and everyday examples. These notes support your thinking; they are not your lab answers. Do not copy your lab answers here — those belong in the Cloud Heights Identity Center.

## 1. Person vs Identity vs Account

```text
A person is: The actual person, like you or me.
An identity is: How a system knows who that person is.
An account is: What they use to log in and get access.
An everyday example that shows the difference: Think about going to work. I'm the person, my employee information tells the company who I am, and my username and password let me log in.
```

## 2. OU vs Group vs Role vs Permission

```text
An organizational unit (OU) is: Basically a folder in Active Directory that helps keep things organized.
A group is: A way to put accounts together so you don't have to manage everybody one by one.
A role is: A set of permissions for what someone needs to do.
A permission is: What they're actually allowed to do, like view something or make changes.
How they fit together in one sentence: The OU keeps things organized, groups put accounts together, roles bundle permissions, and permissions decide what someone can actually do.
```

## 3. Authentication vs Authorization

```text
Authentication means: Proving you're really you.
Authorization means: What you're allowed to do once you're in.
A real-life analogy (for example, an airport or an office building): What you're allowed to do once you're in.
```

## 4. Least Privilege

```text
Least privilege means: Don't give somebody more access than they need.
One place in the labs where giving more access would have been risky: Blair only needed to read Case Summaries. Giving Blair permission to change them would've been unnecessary and could've caused problems.
```

## 5. RBAC

```text
Role-based access control means: Your assigned role helps decide what you're allowed to access.
One role from the labs and what it allowed: The Audit Reviewer role let someone read and export Audit Evidence.
```

## 6. ABAC

```text
Attribute-based access control means: The system looks at certain details before deciding if you should have access.
How it differs from RBAC: RBAC is more about your role. ABAC goes further and checks things like your department, device, or the type of information you're trying to access.
```

## 7. AD DS vs Entra ID

```text
AD DS is: A way companies manage employees, computers, and access on their regular company network.
Microsoft Entra ID is: Similar idea, but mainly for cloud accounts and signing into online services.
One similarity and one difference: Both help manage accounts and access. AD DS focuses more on traditional company networks, while Entra ID focuses on cloud services.
```

## 8. Azure Resource RBAC

```text
What Azure resource RBAC controls: Who can do what with things in Azure, like virtual machines and other cloud resources.
How it relates to what I did in the simulator: It's similar to how I checked which users could access certain resources and what they were allowed to do.
```

## 9. Joiner / Mover / Leaver

```text
Joiner (what happens when someone starts): When someone starts a job, they get their accounts set up and the access they need.
Mover (what happens when someone changes roles): When someone changes jobs or departments, their access needs to change too. They shouldn't keep permissions they no longer need.
Leaver (what happens when someone leaves): When someone leaves, their accounts need to be disabled, their access removed, and any active sessions shut down.
Why the leaver step matters most for security: Because somebody who doesn't work there anymore shouldn't still be able to get into company systems.
```

## 10. Sign-in Logs vs Audit Logs

```text
Sign-in logs show: Who tried to log in, when they tried, and whether it worked.
Audit logs show: What changes or actions happened in the system.
One question each log can answer: Sign-in log: Did this person actually get logged in?
Audit log: Did somebody change this person's access?
```

## 11. Useful Terms and Questions

```text
Terms I want to review: Correlation ID — Like a tracking number that helps connect records.
MFA — Another way to prove it's you besides just a password.
Session — Basically your logged-in connection to a system.
RBAC vs. ABAC — Your role versus extra conditions the system checks.
Least privilege — Only give people what they need.
Questions for my instructor: 1. If two records have the same correlation ID, how do we know whether something suspicious actually happened?
2. If we disable someone's account, do we still need to kick them out of any sessions they already have open?
3. When would a company need ABAC instead of just using roles?
4. What's a simple way to remember the difference between Entra ID and Azure RBAC?
```
