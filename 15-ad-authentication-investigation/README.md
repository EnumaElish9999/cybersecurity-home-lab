# Lab 15 – AD Authentication Investigation

## Objective
Join a client to the domain, generate a real domain logon, and
investigate both halves of what that actually involves: the local
logon event and the Kerberos ticket request on the domain controller.

## Setup
- Domain: cyberlab.local
- New client: Win11-Client (Windows 11 Enterprise, 90-day evaluation),
  static IP 10.10.10.11, DNS pointed at the domain controller (10.10.10.2)
- Accounts used: harish.admin (Domain Admin), jane.doe (standard user)

## Troubleshooting

**Windows 10 Home can't join a domain**
Domain join is unavailable on Home edition entirely — not a setting,
a hard edition limit. Resolved by adding a separate VM running the
free 90-day Windows 11 Enterprise evaluation (no product key
required) as the domain-joined client, keeping the original Windows
10 Home VM untouched for the labs already built on it.

**Domain join failed: "The user's password must be changed before
signing in"**
The account had "user must change password at next logon" still
pending, which the domain-join dialog can't walk you through like a
normal logon screen can. Fixed by resetting the password in Active
Directory Users and Computers with that checkbox unchecked, so the
password was immediately final rather than pending a change.

**"Access is denied (5)" opening the Security log as jane.doe**
Standard domain users can't read the Security log by default — this
needs admin rights. Worked around by using the admin account instead;
the correct real-world fix is adding the account to the built-in
**Event Log Readers** group, which grants log-reading access without
any broader admin rights.

## What I Did
1. Joined Win11-Client to cyberlab.local using harish.admin.
2. Logged in as jane.doe (standard domain user) to generate a real
   domain logon.
3. Reviewed the resulting logon event on the client.
4. Reviewed the resulting Kerberos ticket event on the domain
   controller.

## Findings

**4624 (on the client):**
    New Logon:
        Account Name:    jane.doe
        Account Domain:  CYBERLAB
        Logon Type:      2 (Interactive)
        Elevated Token:  No

Account Domain reads CYBERLAB here, instead of the local computer name
seen in Lab 10 — the visible proof this logon was verified against the
domain controller, not just checked locally.

**4768 (on the domain controller):**
    Account Name:    jane.doe
    Supplied Realm:  CYBERLAB
    Service Name:    krbtgt

Also noted along the way: not every 4624 is a person — computer
accounts (e.g. WIN11-CLIENT$) log on in the background constantly,
using Logon Type 5 (Service).

## What I Learned
- A domain logon is really two events, not one: 4624 on the client
  ("you're allowed in") and 4768 on the domain controller ("here's
  your Kerberos ticket"). The ticket is what enables Single Sign-On —
  once issued, the user can access other domain resources without
  re-entering their password.
- "Realm" is Kerberos's own term for "domain" — same concept, older
  vocabulary, since Kerberos predates Active Directory.
- krbtgt is not a real service — it's the built-in account that signs
  every TGT the domain issues, so it appears in every single 4768
  event regardless of which user is logging in.
- Default audit settings aren't guaranteed for every event type —
  Kerberos auditing specifically needed to be turned on manually here,
  which is a good reminder to verify logging is actually capturing
  what you expect, rather than assuming it is.
- Standard users can't read the Security log by default; the
  Event Log Readers group is the least-privilege way to grant that
  access without full admin rights.
