# Lab 01 — Identity Directory: Who Is Who?

**Learner:** Dominique D Fleming  
**Attempt:** attempt 194ECC · seed chic-w11-v1 · state revision 0 · text revision 21

> Training simulation — fictional Cloud Heights accounts and resources. Your actions change only your Week 11 learning attempt. This is not a live AD domain, Microsoft Entra tenant or Azure subscription.

## Objective

Investigate why one person may have different access in different identity systems.

## My answers

### Difference 1 between the AD and cloud views (what you saw, and why it matters)

I noticed Alex has two separate accounts: ad-alex in Active Directory and cl-alex in Cloud Identity, each with a different username. This matters because even though both accounts belong to Alex, they are managed separately, so checking one doesn't tell me everything about the other.

### Difference 2 between the AD and cloud views

In Active Directory, I saw Alex's account organized in the Response OU, with access through the Response-Team security group. In Cloud Identity, Alex gets access through Response-Operators and the Response Agent role. This matters because the two directories organize and manage access differently, so I need to check both to understand what Alex can actually access.

### What do Junior-Analysts and Case-Readers actually grant? How did you check?

Junior-Analysts gives Blair read access to the Analyst Landing page through the Landing Reader role (AS-01). Case-Readers gives Finley read access to case summaries through the Case Reader role (AS-02). I checked both groups, their members, and the role assignments to see what access they actually have instead of just going by the group names.

### Why does Drew's person record still exist?

Drew's person record still exists because HR keeps employee records even after someone leaves. The HR record shows Drew as Departed, and both accounts (ad-drew and cl-drew) are disabled. This shows that keeping someone's information on file doesn't mean they still have access to the systems. I checked both accounts separately because they don't automatically sync.

## Directory trace

| Concept | Object |
|---|---|
| ou | OU-RESP Response |
| role | RL-LAND Landing Reader @ R-LAND |
| group | GA-RESP Response-Team |
| person | P01 Alex Morgan |
| account | ad-alex (alex@ad.cloudheights.example) |
| identity | ad-alex.department=DEP-RESP |
| permission | queue → read |

## Evidence

- **E01 · EV-194ECC-001** — Directory trace: Alex person, both accounts, OU, one AD grant path, one cloud role path (captured at revision 0)  
  _What this proves:_ These snapshots show Alex Morgan has two separate accounts, one in AD (ad-alex) and one in Cloud Identity (cl-alex). I can see Alex's AD account is in the Response OU and gets access through Response-Team, while the cloud account gets access through Response-Operators and the Response Agent role.  
  See [evidence index](evidence/week11-evidence-index.md#ev-194ecc-001)
- **E02 · EV-194ECC-002** — Comparison of Blair, Finley and Drew (captured at revision 0)  
  _What this proves:_ Comparing these accounts, I observed Blair gets access through Junior-Analysts, while Finley gets different access through Case-Readers. Drew's AD and cloud accounts are both disabled, and his HR record shows he has departed.  
  See [evidence index](evidence/week11-evidence-index.md#ev-194ecc-002)
