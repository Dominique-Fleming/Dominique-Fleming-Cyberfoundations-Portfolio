# Week 7 Notes — Cloud Heights: The Guard Post

**Student Name:** Dominique D Fleming

**Week:** 7

## Firewall and Security Group

```text
A firewall/security group is like a guard. It checks network traffic and decides what is allowed in or out based on the rules that are set.
```

## Rule Anatomy

```text
(priority, direction, source, destination, protocol, port, action)
```

## First Match Wins

```text
Rules are checked from the lowest priority number to the highest. Once the traffic matches a rule, that rule decides Allow or Deny and the system stops checking.
```

## Least Privilege

```text
Only give the access that is actually needed. Don't open access to everyone if only one source needs it.
```

## Testing and Evidence

```text
Don't assume a rule works just because it looks right. Test it and collect evidence. An ALLOWED test proves the right traffic can get through, and a DENIED test proves traffic that should be blocked is actually blocked.
```

## Troubleshooting and Remediation

```text
Check what is working before changing anything. Look at the VM, service, rules, and rule order to find the actual problem. Fix the cause, then test again to make sure it works.
```

## Questions I Still Have

```text
No major questions right now. I want more practice reading rule order and figuring out which rule will match first.
```
