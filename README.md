# PIM & Access Reviews Lab

**Tools:** Microsoft Entra ID, Privileged Identity Management, Identity Governance, Entitlement Management

## Overview

This lab demonstrates how enterprise IAM teams control, certify, and govern access using Microsoft Entra ID Premium P2 features. It covers three core identity governance capabilities:

- **Privileged Identity Management (PIM)** — eliminating standing admin access across multiple roles through just-in-time activation with approval workflows
- **Access Reviews** — running a large-scale certification campaign to recertify access and automatically revoke stale or unjustified assignments
- **Entitlement Management with Separation of Duties (SoD)** — preventing conflicting access combinations from being held by the same identity

## Part 1: Privileged Identity Management (PIM)

### The Problem PIM Solves

In traditional environments, privileged users hold admin roles permanently. If an account is compromised, the attacker has standing access to everything that role permits. PIM removes standing access entirely, users are eligible for a role but must request it, justify it, get it approved, and access expires automatically.

### What Was Built

Converted **4 admin roles** from standing/permanent access to eligible, just-in-time access:

- User Administrator
- Groups Administrator
- Intune Administrator
- Conditional Access Administrator

Each role was configured with identical activation requirements:

| Setting | Value |
|---|---|
| Require Azure MFA on activation | Yes |
| Require justification | Yes |
| Require approval | Yes |
| Approver | PIM Approver |
| Max activation duration | 8 hours |

This represents **100% of admin roles configured in this tenant** operating under JIT access rather than standing assignment.

<img width="1154" height="687" alt="image" src="https://github.com/user-attachments/assets/57ba1a2d-34ef-473b-806a-cd04a2dea424" />

### Full Activation Workflow Tested

PIM Test User requested activation of the User Administrator role, providing justification tied to a real support scenario. The request entered a pending state, was reviewed and approved by PIM Approver, and access was granted for a fixed 2-hour window before automatically expiring.

Every action was captured in PIM Resource Audit logs with timestamps, actor, action type, and status.

## Part 2: Access Reviews at Scale

### The Problem Access Reviews Solve

Users accumulate access over time. Without periodic recertification, organizations end up with access sprawl that violates least privilege. Access Reviews automate the question: does this person still need this access?

### What Was Built

Created a batch of **30 simulated user accounts** and added them to a test group (`GRP-AccessReview-Batch`). Launched a one-time access review campaign (`CORP-AccessReview-Batch-Q3`) targeting all 30 members, with:

| Setting | Value |
|---|---|
| Reviewer | Fernando Huizar |
| Duration | 3 days |
| Auto-apply results | Enabled |
| If no response | Remove access |
| Justification required | Yes |

The review was completed through Microsoft's self-service reviewer portal (myaccess.microsoft.com), the same interface used by real employees and managers to complete access certifications in production environments, distinct from the admin configuration console.

<img width="1005" height="623" alt="image" src="https://github.com/user-attachments/assets/ed9e1d9d-feab-4637-9798-b1758c3db1c6" />

### Results

| Outcome | Count |
|---|---|
| Approved | 22 |
| Denied (explicit) | 5 |
| Removed (no response) | 3 |
| **Total access revoked** | **8** |
| Total reviewed | 30 |

8 of 30 accounts (27%) lost access through this single certification campaign, demonstrating how access reviews catch and remediate unjustified or stale privileged access at scale.

## Part 3: Separation of Duties (Entitlement Management)

### The Problem SoD Solves

Certain role combinations create unacceptable risk when held by the same person, for example, someone who can submit a financial request and also approve it. Separation of Duties policies prevent this by making two access packages mutually exclusive.

### What Was Built

Created two access packages in a shared catalog (`Finance-Access-Catalog`):

- **Finance-Requester-Access** — access for employees who submit finance requests
- **Finance-Approver-Access** — access for employees who approve finance requests

Both packages were configured with:

| Setting | Value |
|---|---|
| Approval required | Yes |
| Access expiration | 90 days |
| Access reviews | Quarterly, 7-day duration |
| If reviewer doesn't respond | Remove access |

A **Separation of Duties rule** was configured marking these two packages as mutually incompatible.

<img width="1151" height="355" alt="image" src="https://github.com/user-attachments/assets/cb9c896a-d7a4-4eb7-9723-4d260488edd1" />

### Verification

Tested the SoD rule directly:

1. Assigned PIM Test User to Finance-Requester-Access — succeeded
2. Attempted to assign the same user to Finance-Approver-Access — **blocked**

Entra returned an explicit failure, citing the conflicting active assignment: `activeAccessPackageAssignmentRequests: ["Finance-Requester-Access"]`.

<img width="325" height="151" alt="image" src="https://github.com/user-attachments/assets/c1dd308c-cadd-47d4-a4ff-a57bff40e596" />

This confirms the policy is not just configured but actively enforced, a user genuinely cannot hold both conflicting access packages at the same time.

## Key IAM Concepts Demonstrated

- **Just-in-Time Access** — no standing privilege across any admin role in the tenant
- **Zero Trust** — every activation requires MFA, justification, and human approval
- **Least Privilege** — access reviews enforce that users only retain access they actively need
- **Separation of Duties** — conflicting access packages are technically prevented from co-assignment, not just policy on paper
- **Audit Readiness** — every PIM activation, access review decision, and SoD enforcement action is logged with actor, timestamp, and outcome

## Tools Used

- Microsoft Entra ID Premium P2
- Privileged Identity Management (PIM)
- Identity Governance — Access Reviews
- Identity Governance — Entitlement Management
- Microsoft Entra admin center / myaccess.microsoft.com
