# Week 5 Notes — The Grid: Addresses, Names, Ports, and Diagnostics

**Student Name:** Dominique D Fleming

**Date Completed:** August 25, 2026

Summarize this week's key concepts in your own words — not copy-pasted definitions.

## Key Concepts This Week

- IP addresses — the dotted-quad number every device on a network needs (`10.20.5.42` on The Grid)
- The subnet mask — the answer to "which addresses are my neighbours?" (`/24` = `255.255.255.0`)
- The default gateway — the door out of your neighbourhood (`10.20.5.1` on The Grid)
- Private vs public addresses — `10.x`, `172.16–31.x`, and `192.168.x` are *inside* addresses
- DNS — the Grid's Directory Board: a name goes in, an IP address comes out
- NXDOMAIN vs a host that resolves but is down — two different failures with two different causes
- DHCP — the Address Office: leases, why addresses change, why a laptop "just works" on a new network
- Ports — the numbered doors on a building: 22 SSH, 53 DNS, 80 HTTP, 443 HTTPS, 3389 RDP, 25 SMTP
- TCP vs UDP — a confirmed conversation vs a shout across the room
- The TCP handshake — SYN → SYN-ACK → ACK (packets 7, 8 and 9 in Lab 03)
- The diagnostic toolkit — `ping` (is it alive?), `traceroute` (where does it stop?), `dig` (what number is behind that name?)
- **THE LADDER RULE** — check yourself → check your gateway → check the target by NAME → check the target by IP → trace the path. *Work outward, one rung at a time, and let the evidence pick the culprit.*

## My Command Table

You learned the same five jobs twice this week — once in bash, once in PowerShell. Fill the pairs in from memory if you can, and check them afterwards. This table is worth keeping.

The bash command and its PowerShell equivalent for each job — show my own address, show my default gateway, test reachability, trace the path, look up a name:

```
Show my own address     ip addr/ipconfig
Show my default gateway ip route/ipconfig
Test reachability       ping/Test-Connection
Trace the path          traceroute/tracert
Look up a name          dig/Resolve-DnsName
```

## In My Own Words

Your machine has three numbers: an address, a subnet mask, and a default gateway. Explain what each one is for, the way you'd explain it to someone who has never heard those words.

```
My address tells me where my device is on the network. The subnet mask helps my device figure out who is in my local neighborhood. The default gateway is the way out when I need to send traffic somewhere outside that neighborhood.
```

What does DNS actually do? Include the difference between a name that comes back "Name or service not known" (NXDOMAIN) and a name that resolves perfectly well to a host that never answers.

```
DNS takes a name and turns it into an IP address. If I get “Name or service not known” or NXDOMAIN, that means DNS could not find that name. If the name resolves to an IP but the machine never answers, then DNS worked and the problem is probably with the machine, the network path, or something blocking it.
```

An IP address gets your traffic to the right building. What does a port number add to that, and why would a defender care how many doors are open?

```
An IP address gets me to the right device, and the port number tells me which door or service I’m trying to reach on that device. A defender cares about open ports because every open door is another possible way in, so more open ports can mean more attack surface and more risk.
```

Write out THE LADDER RULE — all five rungs, in order — and say why running them in that order matters more than running them fast.

```
The Ladder Rule:

Check yourself — Do I have a valid IP address?
Check the gateway — Can I get out at all?
Check the target by name — Does the name respond?
Check the target by IP — Does the number work?
Trace the path — How far do I get?

The order matters because each step rules something out before I move on. Going fast but skipping steps can make me blame the wrong thing and waste more time later.
```

What is DHCP, and why does your laptop get an address automatically on a network it has never joined before, while a server like `grid-dns` keeps the same address permanently?

```
DHCP automatically gives devices the network information they need, like an IP address, when they join a network. Your laptop can get a temporary leased address because it may come and go, while a server like grid-dns keeps a static address so other devices always know where to find it.
```

---

## Submission Checklist

- [x] I summarized each concept in my own words, not copied definitions

- [x] I completed the bash-to-PowerShell command table

- [x] I answered all five "In My Own Words" prompts

- [x] This file is committed to my portfolio repo at `week-05/notes.md`

---

*CyberVisionaries Institute — Cyber Foundations, Tier I*
