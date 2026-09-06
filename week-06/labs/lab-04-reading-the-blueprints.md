# Week 6 Lab 04 — Reading the Blueprints

**Student Name:** Dominique D Fleming

**Date Completed:** September 6, 2026

**Module:** 2 — Networking & Cloud Foundations | **Week:** 6  
**Submission Path:** `week-06/labs/lab-04-reading-the-blueprints.md`

---

> ### 🔒 Cloud Heights Security Rule
> Your Bastion link and Cloud Heights password are **private access credentials**. Never paste either into a worksheet, screenshot, GitHub repository, Circle post, or chat message. When taking screenshots, crop out the browser address bar and all login information.

---

## Overview

**This is a SHORT lab — 15 to 20 minutes.** It is deliberately small. You already have the commands; this lab is about matching a drawing to reality.

The **Cloud Heights Network Blueprint** is displayed at the top of this lab page in the portal. Everything you write about the network's architecture comes from that blueprint or from your own machine — never from a guess.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | Cloud Heights — live Ubuntu 22.04 VM, reached through Azure Bastion |
| Source of truth | The Cloud Heights Network Blueprint shown at the top of this lab page |
| Commands used | `ip addr`, `ip route` |
| Known value | Student subnet: **`10.60.6.0/26`** |

---

## Part A — Read the Drawing

### Step 1 — Record the Architecture Values

From the blueprint at the top of this page, record each value **exactly as drawn**. If a value is not shown on the blueprint, write "not shown on blueprint" — do not guess.

| Item | Value from the blueprint |
| --- | --- |
| VNet name | vnet-cf-labs |
| VNet address space | 10.60.6.0/24 |
| Student subnet range | 10.60.6.0 through 10.60.6.63 |

---

## Part B — Verify Against Your Own Machine

### Step 1 — Confirm Your Address Lives in the Subnet

Run `ip addr` and find your private IPv4 address.

Command and output:

```
ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0d:3a:61:2c:91 brd ff:ff:ff:ff:ff:ff
    inet 10.60.6.36/26 metric 100 brd 10.60.6.63 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::20d:3aff:fe61:2c91/64 scope link 
       valid_lft forever preferred_lft forever
3: enP45768s1: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc mq master eth0 state UP group default qlen 1000
    link/ether 00:0d:3a:61:2c:91 brd ff:ff:ff:ff:ff:ff
    altname enP45768p0s2
```

Your private IP:

```
10.60.6.36/26
```

Explain how you know your address falls inside `10.60.6.0/26` — what range does that prefix actually cover:

```
My address is 10.60.6.36, and the subnet 10.60.6.0/26 covers addresses from 10.60.6.0 through 10.60.6.63. Since .36 falls inside that range, I know my VM is on that student subnet.
```

### Step 2 — Confirm Route Behaviour

Run `ip route`.

Command and output:

```
ip route
```

What the default route tells you about traffic that is not destined for your own subnet:

```
It tells me if traffic has the same subnet then it can get to me. If it has a different subnet then it goes to the default gateway and the gateway sends it where it needs to go.
```

### Step 3 — Capture Your Evidence

**Required filename:** `blueprint-verified.png`

This must be **your own `ip addr` and `ip route` output** — not a re-screenshot of the blueprint. Crop out the address bar and any login information.

![Blueprint verified — my address inside the student subnet](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-06/blueprint-verified.png)

---

## Part C — How Traffic Actually Moves

### Step 1 — No Public IP

Your VM has a private address and **no public IP**. Explain what that means for who can reach it directly from the internet:

```
The private address means only those inside my VM or given access can have access to me. Having no public IP address means that there is no way to reach me or locate me like they would on a pubkic-facing server.
```

### Step 2 — Outbound vs. Inbound

Outbound internet traffic from your VM leaves through address **translation (NAT)**. Inbound access for you arrives through **Azure Bastion**, not through a public address on the VM.

Explain both directions in your own words:

```
Outbound traffic goes through NAT, which is like a front desk changing my private room number to the building’s public address before sending the message outside. Inbound access comes through Azure Bastion, which is like a guarded front desk checking that I’m allowed in before opening a path to my VM. So NAT helps my private VM talk out, while Bastion gives me a controlled way to get in.
```

### Step 3 — The Guard Post You Do Not Touch Yet

Each student machine sits behind its own **network security group** — a per-student guard post that decides what traffic is allowed in.

**In Week 6 you do not configure it.** Week 7 is when you take control of those rules.

Write one sentence naming what the guard post does and one sentence stating what you are *not* doing with it this week:

```
The guard post checks inbound traffic and only allows what the network security rules permit to reach my VM. This week I’m not changing or managing those rules yet — that comes in Week 7.
```

---

## Analysis Questions

**Analysis Question 1.** Why would an organization put every student machine in one small subnet instead of giving each machine a public address? *(Minimum 3 sentences.)*

```
An organization would put all the student machines in one small subnet so they are easier to organize, manage, and keep inside the same controlled network space. By not giving each VM a public IP address, the organization also reduces the attack surface because those machines are not directly exposed to the internet. If every machine had its own public address, attackers would have more possible doors to find and target.
```

**Analysis Question 2.** Segmentation means separating a network into parts that cannot freely reach each other. Give one concrete benefit of segmentation during a security incident. *(Minimum 3 sentences.)*

```
Segmentation can help contain a security incident so it does not spread freely to other parts of the network. That protects other students or systems from being reached as easily. It also gives defenders a smaller fire to deal with instead of trying to control a problem across the whole network.
```

**Analysis Question 3.** A diagram and a live machine disagree about an address range. Which do you trust, what do you do next, and why? *(Minimum 2 sentences.)*

```
I would trust the live machine more as current evidence, but I’d verify it with commands like ip addr and ip route and compare that against the diagram. If they still disagree, I’d document the mismatch and report it instead of guessing which one is wrong.
```

---

## Submission Checklist

- [x] VNet name, address space, and subnet range recorded from the blueprint (Part A)

- [x] `ip addr` run and own private IP confirmed inside `10.60.6.0/26` (Part B, Step 1)

- [x] `ip route` run and default route behaviour explained (Part B, Step 2)

- [x] `blueprint-verified.png` captured from your own terminal, cropped, uploaded to `assets/screenshots/week-06/` (Part B, Step 3)

- [x] Private address / NAT / Bastion explained (Part C, Steps 1–2)

- [x] Per-student guard post identified — and explicitly not configured this week (Part C, Step 3)

- [x] All three Analysis Questions answered (minimum sentence counts met)

- [x] This file is committed to your portfolio repo at `week-06/labs/lab-04-reading-the-blueprints.md`

---

## GitHub Commit Subsection

1. Open **Week 6 → Lab 04: Reading the Blueprints** in the Lab Portal.
2. Fill in the worksheet fields and upload `blueprint-verified.png` to `assets/screenshots/week-06/`.
3. Click **Submit to GitHub** — the Portal commits to `week-06/labs/lab-04-reading-the-blueprints.md`.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
