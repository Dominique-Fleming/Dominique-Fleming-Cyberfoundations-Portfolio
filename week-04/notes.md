# Week 4 Notes — Permissions, Searching, and Virtual Machines

**Student Name:** Dominique D Fleming

**Date Completed:** August 17, 2026

Summarize this week's key concepts in your own words — not copy-pasted definitions.

## Key Concepts This Week

- File permissions: read/write/execute × owner/group/other, and reading `ls -l`
- Changing permissions with `chmod` (symbolic and numeric) — and THE GATEKEEPER'S RULE
- Windows ACLs, read with `Get-Acl` (the real-world `icacls` tool does the same job, but is not available in the simulator)
- Wildcards (`*`, `?`, `[ ]`) and searching inside files with `grep`/`Select-String`
- Virtual machines: host vs. guest, the hypervisor, Type 1 vs. Type 2, isolation
- The VM lifecycle: create, start, stop (deallocate), snapshot, delete — and what each costs
- Golden snapshots — how your Weeks 6–12 lab machines are made

## In My Own Words

**Decode `-rw-r-----` audience by audience: who can do what to this file?**

```
The owner can read and write, the group (team) can read and everybody else(other) has no permissions
```

**What is a hypervisor, and what are its two jobs?**

```
A hypervisor is software that manages the resources of a host computer. Its first job is to divide and allocate the host’s real CPU, RAM, and disk resources between each guest, or VM. Its second job is to keep the guests isolated from each other, so one VM cannot see or impact another VM and problems stay contained to that guest
```

**A stopped VM still costs a little money. What is it paying for, and what's the only way to reach a true zero?**

```
A stopped VM is still paying the “locker fee” because even though it is no longer using CPU or active compute resources, its disk storage still exists and is being kept for you. The only way to reach a true zero is to delete the VM and its disk completely.
```

---

## Submission Checklist

- [x] I summarized each concept in my own words, not copied definitions

- [x] I answered all three "In My Own Words" prompts

- [x] This file is committed to my portfolio repo at `week-04/notes.md`
