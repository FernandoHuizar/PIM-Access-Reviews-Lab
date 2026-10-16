# PIM and Access Reviews Lab

**The problem:** admins often keep their admin rights all the time, people collect access they no longer need, and sometimes one person ends up able to both request and approve the same thing.

**What I did:** in Microsoft Entra ID, I made admin rights temporary and approval-based, ran an access review on 30 users that removed 8, and set a rule that blocks one person from holding two conflicting kinds of access.

*Screenshot lab from earlier in my learning. My main project is [Havlund Pharma](https://github.com/FernandoHuizar/havlund-identity-lifecycle).*

## IAM ideas this lab shows

| Idea | What it means | Where it shows up here |
|---|---|---|
| Least privilege | People get only the access they need, for only as long as they need it | Admin roles are off until requested |
| Just-in-time admin access | Admin rights are turned on for a few hours when needed, then turn off by themselves | 4 admin roles set up this way with PIM |
| Access review | Regularly asking "does this person still need this?" | 30 users reviewed, 8 lost access |
| Separation of duties | One person can't hold two jobs that should check each other | Can't be both a finance requester and approver |
| Audit trail | A record of who did what and when | Every request, approval and review decision is logged |

## Part 1: Admin rights only when needed (PIM)

**Why:** if an account with permanent admin rights gets stolen, the attacker gets those rights too. With PIM (Privileged Identity Management), a user is *eligible* for a role but has to ask for it each time.

I changed 4 admin roles from permanent to "request when needed": User Administrator, Groups Administrator, Intune Administrator and Conditional Access Administrator. Each one needs:

| Setting | Value |
|---|---|
| MFA when turning it on | Yes |
| A reason | Yes |
| Approval | Yes, by a separate approver account |
| Longest it can stay on | 8 hours |

<img width="1154" height="687" alt="PIM role settings" src="https://github.com/user-attachments/assets/57ba1a2d-34ef-473b-806a-cd04a2dea424" />

**Test:** a test user asked for User Administrator with a reason. The approver approved it, the user had the role for 2 hours, and then it turned off by itself. Each step showed up in the PIM audit log.

## Part 2: Access review on 30 users

**Why:** access piles up over time. A review asks the person responsible to confirm each person still needs it.

I created 30 test users in one group and started a review of that group:

| Setting | Value |
|---|---|
| Reviewer | Me |
| Length | 3 days |
| Apply results automatically | Yes |
| If the reviewer doesn't answer | Remove access |
| Reason required | Yes |

I did the review from the My Access portal, the same page a real manager would use.

<img width="1005" height="623" alt="Access review in My Access" src="https://github.com/user-attachments/assets/ed9e1d9d-feab-4637-9798-b1758c3db1c6" />

**Results:**

| Outcome | Count |
|---|---|
| Kept | 22 |
| Denied | 5 |
| Removed because no one answered | 3 |
| **Access removed in total** | **8 of 30** |

## Part 3: Separation of duties

**Why:** someone who can submit a finance request shouldn't also be able to approve it.

I made two access packages (bundles of access people can request): **Finance-Requester-Access** and **Finance-Approver-Access**. Both need approval, expire after 90 days and get reviewed every quarter. Then I marked them as not allowed together.

<img width="1151" height="355" alt="Separation of duties setting" src="https://github.com/user-attachments/assets/cb9c896a-d7a4-4eb7-9723-4d260488edd1" />

**Test:**
1. Gave a test user Finance-Requester-Access. It worked.
2. Tried to give the same user Finance-Approver-Access. **Entra blocked it** and named the conflicting package.

<img width="325" height="151" alt="Assignment blocked" src="https://github.com/user-attachments/assets/c1dd308c-cadd-47d4-a4ff-a57bff40e596" />

## What I learned

- Temporary admin rights cut risk a lot, but approvers have to actually check the reason.
- "Remove if no one answers" is a strong setting. It cleans up access, but managers need a heads-up before the review starts.
- A separation of duties rule should be tested, not just set. I tried to break it to prove it works.

## Built with

Microsoft Entra ID P2 (trial), Privileged Identity Management, Access Reviews, Entitlement Management, My Access portal.
