# Week 6 Lab 05 — Layer Detective

**Student Name:** Dominique D Fleming

**Date Completed:** September 6, 2026

**Module:** 2 — Networking & Cloud Foundations | **Week:** 6  
**Submission Path:** `week-06/labs/lab-05-layer-detective.md`

---

## Overview

**This is a SHORT lab — 20 to 30 minutes — and it needs no VM.** No Cloud Heights session, no simulator, no screenshot. This is a thinking lab: you take the evidence you have already collected in Weeks 5 and 6 and sort it into layers.

This is an **independent** lab.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | This worksheet only — nothing to start, nothing to connect to |
| Prerequisite | Week 5 labs and Week 6 Labs 01–04 |
| Screenshot | None required |

---

## Part A — The Seven-Row Table

Fill in every row. For the last column, name one **real thing you personally saw** in Weeks 5–6 that belongs at that layer.

| # | Layer name | One-line job | Real thing from Weeks 5–6 |
| --- | --- | --- | --- |
| 7 | Application | What I actually use/interact with | DNS lookup, SSH login, HTTP request |
| 6 | Presentation | Makes data readable/usable, including encryption | HTTP readable vs TLS scrambled |
| 5 | Session | Keeps the conversation/session going | Bastion/SSH session |
| 4 | Transport | Gets traffic to the right service using ports/TCP/UDP | DNS packet using source port 54211 → destination port 53 |
| 3 | Network | IP addressing and routing between networks | IP addresses, subnets, gateways, routing |
| 2 | Data Link | Moves data across the local hop using MAC addresses | Ethernet II / MAC addresses in Packet Inspector |
| 1 | Physical | Carries the actual signal | Your real Wi-Fi/network connection carrying the traffic |

---

## Part B — Case Files

For each case, name the layer where the problem lives, and name the evidence proving the layers **below** it were already working.

### Case File 1 — The Name That Went Nowhere

A hostname lookup fails, but pinging the machine's IP address directly succeeds.

Layer:

```
Layer 7 — Application
```

Evidence that the layers below were working:

```
The direct IP ping worked, so that proves the lower networking pieces were already doing their job: the signal moved, the local link worked, and IP routing/addressing worked well enough to reach the machine. 
```

### Case File 2 — Permission Denied

`ssh` to a host returns `Permission denied` after a password prompt.

Layer:

```
Layer 7- Application
```

Evidence that the layers below were working:

```
Application failed because the SSH login was denied at the authentication step. The password prompt proves the lower layers were already working because the connection reached the machine, hit port 22, and got far enough for SSH to ask for credentials.
```

### Case File 3 — The Cable Story

A machine reports no link on its interface and has no address at all.

Layer:

```
Layer 1 — Physical
```

Evidence and reasoning:

```
Since Layer 1 is the bottom layer, there are no lower layers to prove were working, and having no address fits because the machine never got past the physical connection.
```

### Case File 4 — Ping Works, The Page Does Not

`ping` to a server succeeds, but `curl http://<that server>` returns nothing useful.

Layer:

```
Layer 4 Transport
```

Evidence that the layers below were working:

```
Transport failed because the server answered ping, but the HTTP service on port 80 did not give a useful response. The successful ping proves the lower layers were already working because the machine could be reached by IP, so the problem is higher up at the port/service level.
```

### Case File 5 — Wrong Neighbourhood

A machine has an address, but its default route points somewhere that cannot forward its traffic.

Layer:

```
Layer 3 Network
```

Evidence and reasoning:

```
Network failed because the machine had an IP address, but its default route pointed somewhere that could not forward the traffic. Having an address shows the lower layers were working enough for the machine to be connected locally, but the traffic failed when it tried to leave through the bad route.
```

---

## Part C — The Silent Gateway Case

In Lab 03 the Azure default gateway did not answer your ping. However, your VM had a valid default route configured, and your local communication with the Grid Beacon — the ping replies, the HTTP banner, and `TRACE ID: CF-NET-0604` — succeeded.

A failed gateway ping is one piece of evidence — not automatically proof of a gateway or network failure. But the evidence you weigh against it has to be the right kind of evidence.

The Grid Beacon at `10.60.6.4` sits on the same local subnet as your VM (`10.60.6.0/26`). Reaching it proves **local-subnet connectivity** — that traffic never crosses the default gateway, so beacon success alone cannot prove the gateway forwarded anything. Your `ip route` output proves a **default route is configured** — your VM knows where it intends to send non-local traffic — but it does not prove the gateway forwarded that traffic. The evidence that demonstrates the **default path is functioning** is successful communication with a destination outside `10.60.6.0/26`, such as the outbound internet access through NAT that you examined in Lab 04.

### Step 1 — Rule on the Case

Is the failed gateway ping enough evidence to declare a network-layer failure? Explain your answer using the other evidence you collected. In your response, distinguish between:

- evidence that proves **local-subnet connectivity**
- evidence that proves a **default route is configured**
- evidence that supports **successful off-subnet connectivity**

```
No, the failed gateway ping is not enough to declare a Layer 3 failure. 

The Grid Beacon proved my VM had local-subnet connectivity because it could reach 10.60.6.4 on the same subnet, while ip route proved a default route was configured through the gateway. The evidence for off-subnet connectivity was successful outbound communication beyond 10.60.6.0/26, which showed traffic could actually use the default path and leave the local subnet.
```

### Step 2 — Name the Correct Conclusion

For each of these four results, state what it actually proves: the Grid Beacon at `10.60.6.4` answering, the default route shown by `ip route`, a successful connection to a destination outside your local subnet, and the gateway's failed ping. Then state the rule you would give a junior colleague about the difference between an observation ("the gateway did not answer my ICMP probe") and a diagnosis ("the gateway is broken"):

```
The Grid Beacon answering proves local-subnet connectivity because 10.60.6.4 is on the same subnet as my VM. ip route proves my VM has a default route configured, while a successful connection to something outside 10.60.6.0/26 proves the off-subnet path is actually working. The gateway not answering ping only proves it did not reply to that ICMP request — it does not prove the gateway is broken.

Observation = what I actually saw. Diagnosis = what I can prove from all the evidence.
```

---

## Part D — Two Models, One Job

The OSI model has seven layers. The practical TCP/IP model most engineers speak day to day has four or five.

### Step 1 — Map Them

Briefly show how the seven OSI layers collapse into the practical model:

```
TCP/IP Application = OSI 7 Application + 6 Presentation + 5 Session
TCP/IP Transport = OSI 4 Transport
TCP/IP Internet = OSI 3 Network
TCP/IP Network Access = OSI 2 Data Link + 1 Physical
```

### Step 2 — When Each Is Useful

Explain when the seven-layer vocabulary helps and when the practical model is the better tool:

```
The seven-layer OSI model is useful when I need to be more specific about where a problem lives, especially for troubleshooting, interviews, certifications, or talking with other tech people. The TCP/IP model is better for everyday work because it groups the same jobs into fewer buckets and is faster to use when I’m trying to figure out what is actually broken.
```

---

## Analysis Questions

**Analysis Question 1.** Explain the Ladder Rule using layer language. What does "test the near thing first" mean when the rungs are layers? *(Minimum 3 sentences.)*

```
The Ladder Rule means I start at the lower layers first and work my way up. Each layer I prove is working lets me rule it out and move higher. That helps me narrow down the problem with evidence instead of guessing and blaming the wrong layer.
```

**Analysis Question 2.** Why is "which layer is this?" a faster question than "what is broken?" when you are under pressure? *(Minimum 3 sentences.)*

```
Asking “which layer is this?” helps me figure out where to start looking instead of guessing at everything at once. It also helps me avoid wasting time using tools or commands that do not match the problem. Once I know the layer, I can focus on that area and rule things out faster.
```

**Analysis Question 3.** Pick one case file from Part B and describe the very next command you would run to confirm your ruling, and what result would change your mind. *(Minimum 2 sentences.)*

```
Case File 5 — Wrong Neighbourhood. I would run ip route next to check the default route and see exactly where the machine is trying to send off-subnet traffic. If the default route was correct and traffic still failed, I’d change my mind and start looking past the route itself, like the gateway or something farther down the path.
```

---

## Submission Checklist

- [x] All seven rows of the OSI table completed with a real Week 5–6 anchor each (Part A)

- [x] All five case files given a layer and supporting evidence (Part B)

- [x] Silent gateway case ruled on correctly (Part C)

- [x] OSI vs. practical TCP/IP model compared (Part D)

- [x] All three Analysis Questions answered (minimum sentence counts met)

- [x] No screenshot required for this lab

- [x] This file is committed to your portfolio repo at `week-06/labs/lab-05-layer-detective.md`

---

## GitHub Commit Subsection

1. Open **Week 6 → Lab 05: Layer Detective** in the Lab Portal.
2. Fill in the worksheet fields.
3. Click **Submit to GitHub** — the Portal commits to `week-06/labs/lab-05-layer-detective.md`.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
