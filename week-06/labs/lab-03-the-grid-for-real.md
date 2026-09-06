# Week 6 Lab 03 — The Grid, For Real

**Student Name:** Dominique D Fleming

**Date Completed:** September 5, 2026

**Module:** 2 — Networking & Cloud Foundations | **Week:** 6  
**Submission Path:** `week-06/labs/lab-03-the-grid-for-real.md`

---

> ### 🔒 Cloud Heights Security Rule
> Your Bastion link and Cloud Heights password are **private access credentials**. Never paste either into a worksheet, screenshot, GitHub repository, Circle post, or chat message. When taking screenshots, crop out the browser address bar and all login information.

---

## Overview

In Week 5 you ran `ip addr`, `ip route`, `ping`, and `traceroute` in a simulator that always behaved. Today you run the same toolkit against real cloud infrastructure that does **not** always behave the way the textbook implies — and you learn to tell "broken" apart from "normal."

This is an **independent** lab. It tells you what to accomplish; you choose the commands. Expect about 40 minutes.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | Cloud Heights — live Ubuntu 22.04 VM, reached through Azure Bastion |
| Commands used | `ip addr`, `ip route`, `ping`, `traceroute`, `curl` |
| Known-good target | **Grid Beacon — `10.60.6.4`** |
| Prerequisite | Week 6 Labs 01–02 |

---

## Part A — Where You Actually Are

### Step 1 — Read Your Own Address

Run the command that lists your interfaces and addresses.

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
3: enP13815s1: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc mq master eth0 state UP group default qlen 1000
    link/ether 00:0d:3a:61:2c:91 brd ff:ff:ff:ff:ff:ff
    altname enP13815p0s2
```

Your private IPv4 address and prefix length:

```
10.60.6.36/26
```

### Step 2 — Read Your Route

Run the command that shows the routing table.

Command and output:

```
ip route
default via 10.60.6.1 dev eth0 proto dhcp src 10.60.6.36 metric 100 
10.60.6.0/26 dev eth0 proto kernel scope link src 10.60.6.36 metric 100 
10.60.6.1 dev eth0 proto dhcp scope link src 10.60.6.36 metric 100 
168.63.129.16 via 10.60.6.1 dev eth0 proto dhcp src 10.60.6.36 metric 100 
169.254.169.254 via 10.60.6.1 dev eth0 proto dhcp src 10.60.6.36 metric 100 
```

Your default gateway:

```
10.60.6.1
```

### Step 3 — Compare to Week 5

Compare this live Ubuntu output to what the CLI Simulator produced in Week 5. What looks the same, what looks different, and what surprised you:

```
The same part is that I’m still looking for my IP address, subnet, and default gateway just like I did in the simulator. The different part is the real Ubuntu output is way messier with lots of extra details that weren’t in the simulator. What surprised me most was that the gateway can show up normally in ip route with the words "default" next to it.
```

---

## Part B — The Gateway That Does Not Answer

### Step 1 — Ping the Gateway

Ping the default gateway address you recorded. Let it run a few seconds, then stop it.

Command and output:

```
ping 10.60.6.1

--- 10.60.6.1 ping statistics ---
196 packets transmitted, 0 received, 100% packet loss, time 199709ms
```

### Step 2 — Interpret It Correctly

You almost certainly got **no replies**. In Azure, the platform gateway commonly does not answer ICMP. This is **expected platform behaviour** and by itself proves nothing about whether your machine or network is broken.

Explain why "the gateway did not answer ping" is weak evidence:

```
The gateway not answering ping is weak evidence because Azure’s gateway can stay silent even when it is working normally. ip route already shows that the path through 10.60.6.1 exists, so no ping reply by itself does not prove the network is broken.
```

---

## Part C — The Known-Good Target

The **Grid Beacon** at `10.60.6.4` is a machine that is known to be up and known to answer. When your first probe fails, you test against something known-good before you conclude anything.

### Step 1 — Ping the Beacon

```
ping 10.60.6.4
```
Output:

```
--- 10.60.6.4 ping statistics ---
81 packets transmitted, 81 received, 0% packet loss, time 80099ms
rtt min/avg/max/mdev = 0.940/1.264/9.077/0.920 ms
```

### Step 2 — Trace the Path

```
traceroute 10.60.6.4
```
Output:

```
traceroute to 10.60.6.4 (10.60.6.4), 30 hops max, 60 byte packets
 1  grid-beacon.internal.cloudapp.net (10.60.6.4)  1.310 ms  1.306 ms  1.519 ms
```

### Step 3 — Ask the Application

```
curl http://10.60.6.4
```
Output:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GRID BEACON | CVI CyberFoundations</title>
    <style>
        body {
            background: #071426;
            color: #d9f7ef;
            font-family: monospace;
            max-width: 850px;
            margin: 80px auto;
            padding: 30px;
        }
        .beacon {
            border: 1px solid #31d6a6;
            padding: 35px;
        }
        h1 { color: #31d6a6; }
        .label { color: #8ca8ff; }
        .status { color: #31d6a6; }
        .classified {
            margin-top: 30px;
            border-top: 1px solid #31445e;
            padding-top: 20px;
        }
    </style>
</head>
<body>
<div class="beacon">
  <h1>GRID BEACON</h1>

    <p><span class="label">NODE:</span> grid-beacon</p>
    <p><span class="label">NETWORK:</span> CVI Training Grid</p>
    <p><span class="label">STATUS:</span>
       <span class="status">ONLINE</span></p>

    <p>
        Network beacon established.<br>
        If you reached this node, your route is operational.
    </p>

    <div class="classified">
        <p>INVESTIGATION CHECKPOINT</p>

        <p>
            Observe the path that brought you here.
            The destination is only part of the story.
        </p>

        <p>TRACE ID: CF-NET-0604</p>
    </div>

</div>
</body>
</html>
```

> ### ⚠️ Grid Beacon not responding?
> The Grid Beacon is shared course infrastructure and should normally be available. First, confirm your Cloud Heights VM shows **Running** and that you completed the preceding network checks. Then retry the command once after a minute or two.
>
> If the Grid Beacon still does not respond, **stop this part of the lab and contact your instructor.** Record that the shared service was unavailable; do not treat the result as evidence that your VM or your work is incorrect.
>
> Do not change networking, NSGs, firewall rules, routes, DNS, or any Azure settings to try to reach the beacon.
>
> *Instructor note: a confirmed Grid Beacon outage is an environment issue, not a student error. Affected students may complete this portion of Lab 03 after the service is restored, with no penalty.*

### Step 4 — Record the Application Evidence

The beacon returns a banner and a trace ID. Record exactly what you received:

```
Grid Beacon TRACE ID: CF-NET-0604
```

Explain the difference between what the `ping` proved and what the `curl` proved:

```
ping proved I could reach the grid-beacon and get a reply back. curl proved there was an actual service there that responded with web content, so it showed me more than just “the machine answered.” Together they proved that the network and the destination were successful.
```

### Step 5 — Capture Your Evidence

Two screenshots, both cropped to the terminal only:

**Required filename:** `vm-toolkit-live.png` — your `ip addr` and `ip route` output

![Live VM toolkit — ip addr and ip route](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-06/vm-toolkit-live.png)

**Required filename:** `beacon-reply.png` — your beacon ping/traceroute/curl evidence

![Grid Beacon reply](https://raw.githubusercontent.com/Dominique-Fleming/Dominique-Fleming-Cyberfoundations-Portfolio/refs/heads/main/assets/screenshots/week-06/beacon-reply.png)

---

## Part D — Rewrite the Ladder Rule

Week 5 taught the Ladder Rule: test the near thing before the far thing. Real infrastructure adds a wrinkle — a silent rung is not automatically a broken rung.

Rewrite the Ladder Rule in your own words so that it survives real cloud infrastructure. Your version must include both **route/path evidence** and **a known-good target**:

```
Start close and work my way out. First make sure my own IP and route look right, then use that route info to prove there is a path out, and test a known-good target to make sure something past that path actually answers. After that, check the problem by name, then by IP, then trace the path if I still need to narrow it down.
```

---

## Analysis Questions

**Analysis Question 1.** Your ping to the gateway failed and your ping to the beacon succeeded. What does that pair of results, taken together, prove about your machine's networking? *(Minimum 3 sentences.)*

```
The gateway not answering by itself did not prove anything was broken. The beacon answering proved my machine still had a working path through the network and could reach another device. Together, that shows my networking was healthy and the silent gateway was just normal Azure behavior. 

```

**Analysis Question 2.** Why is `traceroute` useful even when `ping` already answered? What extra thing does it show you? *(Minimum 2 sentences.)*

```
ping tells me the target answered, but traceroute shows me the path the traffic took to get there. That extra path info helps me see where traffic is traveling and where it might stop if there is a problem. 
```

**Analysis Question 3.** A service is unreachable and ping to it succeeds. Where would you look next, and why is "the network is fine" an incomplete answer? *(Minimum 3 sentences.)*

```
If ping works, I know I can reach the machine, so I’d look next at the port or service itself. I’d check whether the right service is actually listening and whether something like a firewall is blocking that port. Saying “the network is fine” is incomplete because the path can work while the actual service on top of it is still broken.
```

**Analysis Question 4.** Something already controls what is allowed to reach your machine in Cloud Heights. If you could decide those rules, what would you want to allow, what would you want to block, and who in an organization should get to make that decision? *(Minimum 3 sentences.)*

```
I’d only allow the traffic the machine actually needs, like SSH on port 22 for approved admin access, and block anything else that has no reason to be open. I’d want those rules decided by the security/network team with the system owner, not just whoever happens to be using the machine.
```

---

## Submission Checklist

- [x] `ip addr` output recorded and own private IP/prefix identified (Part A)

- [x] `ip route` output recorded and default gateway identified (Part A)

- [x] Live output compared to the Week 5 simulator (Part A, Step 3)

- [x] Gateway pinged and the silent result interpreted correctly (Part B)

- [x] Beacon `ping`, `traceroute`, and `curl` all run and recorded (Part C)

- [x] Beacon banner and TRACE ID recorded (Part C, Step 4)

- [x] `vm-toolkit-live.png` and `beacon-reply.png` captured, cropped, uploaded to `assets/screenshots/week-06/` (Part C, Step 5)

- [x] Ladder Rule rewritten with route evidence + known-good target (Part D)

- [x] All four Analysis Questions answered (minimum sentence counts met)

- [x] This file is committed to your portfolio repo at `week-06/labs/lab-03-the-grid-for-real.md`

---

## GitHub Commit Subsection

1. Open **Week 6 → Lab 03: The Grid, For Real** in the Lab Portal.
2. Fill in the worksheet fields and upload both screenshots to `assets/screenshots/week-06/`.
3. Click **Submit to GitHub** — the Portal commits to `week-06/labs/lab-03-the-grid-for-real.md`.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
