PIM & Access Reviews Lab

Microsoft Entra ID | Privileged Identity Management | Identity Governance


What This Lab Demonstrates

This lab simulates how enterprise IAM teams control and audit privileged access using Microsoft Entra ID. It covers two core identity governance capabilities:


Privileged Identity Management (PIM) — eliminating standing admin access through just-in-time role activation with approval workflows
Access Reviews — automating periodic recertification of group memberships and privileged role assignments to enforce least privilege over time


Both are P2-licensed features used in real enterprise environments for SOX compliance, zero trust enforcement, and audit readiness.


Environment


Tenant: fernandotech187.onmicrosoft.com
License: Microsoft Entra ID Premium P2
Platform: Microsoft Entra admin center (entra.microsoft.com)
Users created for this lab:

pim.user@fernandotech187.onmicrosoft.com — test user requesting elevated access
pim.approver@fernandotech187.onmicrosoft.com — approver reviewing activation requests
review.user@fernandotech187.onmicrosoft.com — member included in access review






Part 1: Privileged Identity Management (PIM)

The Problem PIM Solves

In traditional AD environments, privileged users hold admin roles permanently. If an account is compromised, the attacker has standing access to everything that role permits. PIM removes standing access entirely. Users are eligible for a role but must request it, justify it, get it approved, and the access expires automatically.

What Was Built

Eligible Role Assignment

Assigned pim.user as eligible for the User Administrator role rather than granting it permanently. Before PIM, this user had zero active role assignments.

Activation Settings Configured on User Administrator Role

SettingValueMax activation duration8 hoursRequire MFA on activationYesRequire justificationYesRequire approvalYesApproverpim.approver

Full Activation Workflow Tested


pim.user navigated to PIM > My Roles and clicked Activate on User Administrator
Entered activation duration of 2 hours and justification: "Resetting password for locked out user in HR department per ticket INC0042"
Request entered pending state — no access granted yet
pim.approver reviewed the request in PIM > Approve Requests, verified the justification, and approved with reason: "Verified ticket, approving 2 hour User Administrator access"
Role activated — pim.user home screen updated from "No roles assigned" to showing 1 high privileged role assignment
Access set to expire automatically at end of 2-hour window


Audit Trail

Every action was captured in PIM Resource Audit logs with timestamps, actor, action type, and status — demonstrating the compliance-ready logging that audit teams rely on for SOX and access control verification.


Part 2: Access Reviews

The Problem Access Reviews Solve

Users accumulate access over time. Someone changes roles, a project ends, a contractor stays too long. Without periodic recertification, organizations end up with access sprawl that violates least privilege. Access Reviews automate the question: does this person still need this access?

Review 1: Group Membership Review

Group reviewed: GRP-Finance-Users (security group with pim.user and review.user as members)

SettingValueReview nameCORP-Finance-Users-Access-ReviewScopeAll usersReviewerFernando Huizar (Global Admin)FrequencyQuarterlyDuration3 daysEndAfter 3 occurrencesAuto-apply resultsEnabledIf no responseRemove accessDecision helpersFlag users inactive 30+ days, User-to-Group affiliationJustification requiredYes

Auto-apply and remove-on-no-response means access is enforced automatically without manual follow-up. If a reviewer doesn't act within 3 days, the user loses access by default — the secure outcome.

Review 2: Privileged Role Review

Created directly from PIM > Entra ID Roles > Access Reviews to review who holds the User Administrator role and whether continued assignment is justified.

SettingValueReview nameCORP-Privileged-Role-Access-ReviewRole reviewedUser AdministratorAssignment typeAll active and eligible assignmentsReviewerFernando Huizar (Global Admin)FrequencyQuarterlyDuration3 daysEndAfter 3 occurrencesAuto-apply resultsEnabledIf no responseRemove accessShow recommendationsEnabledJustification requiredYes


Key IAM Concepts Demonstrated

Just-in-Time Access — No standing privilege. Users activate roles only when needed, for limited durations, with full audit trail.

Zero Trust — No user or role is trusted by default. Every activation requires MFA, justification, and human approval before access is granted.

Least Privilege — Access Reviews enforce that users only retain access they actively need. Unused or unreviewed access is removed automatically.

Separation of Duties — The person requesting access (pim.user) cannot approve their own request. A separate approver (pim.approver) controls the gate.

Audit Readiness — Every PIM activation, approval, and denial is timestamped and logged. Every access review decision is recorded. This is the paper trail compliance teams require for SOX and internal audits.

Identity Governance — Access isn't just provisioned once and forgotten. It's reviewed, recertified, and revoked on a scheduled cadence aligned to business need.


Tools Used


Microsoft Entra ID Premium P2
Privileged Identity Management (PIM)
Identity Governance — Access Reviews
Microsoft Entra admin center


