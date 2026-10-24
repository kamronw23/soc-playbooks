# Playbook 01: Reported Phishing Email

| | |
|---|---|
| **Trigger** | A user reports a suspicious email, or the email gateway raises an alert |
| **ATT&CK** | T1566.001 Spearphishing Attachment · T1566.002 Spearphishing Link · T1078 Valid Accounts (if creds were entered) |
| **Severity guide** | Low = no interaction · Medium = link clicked · High = credentials entered or attachment opened · Critical = many users affected or executive targeted |
| **Tools** | [`email_header_analyzer.py`](https://github.com/kamronw23/security-scripts/tree/main/phishing), [`ioc_extractor.py`](https://github.com/kamronw23/security-scripts/tree/main/threat-intel), VirusTotal, URLScan.io, AbuseIPDB, sandbox (Any.Run / Joe Sandbox) |

---

## 1. Triage (first 15 minutes)

- [ ] Get the **original email as `.eml`** (not a forward, since forwarding strips the headers)
- [ ] Ask the user: *Did you click? Enter a password? Open the attachment? Reply?*
- [ ] Run the header analyzer: `python3 email_header_analyzer.py reported.eml`
  - SPF / DKIM / DMARC pass or fail?
  - From vs Return-Path vs Reply-To domains match?
  - Display-name spoofing or a lookalike domain (`paypa1`, `rnicrosoft`)?
- [ ] Extract IOCs: `python3 ioc_extractor.py reported.eml --defang`

## 2. Analysis

- [ ] **URLs:** submit to URLScan.io and VirusTotal. *Never open them in your own browser.*
- [ ] **Attachments:** take the SHA-256 hash and search VirusTotal. If unknown, detonate in a sandbox.
- [ ] **Sender IP / domain:** AbuseIPDB reputation, WHOIS domain age (under 30 days is a red flag)
- [ ] **Scope:** search the mail gateway / M365 Message Trace for the same sender, subject, or URL. *Who else got it?*

**Verdict:** ☐ Phishing ☐ Spam / marketing ☐ Legitimate (false positive)

## 3. Containment

- [ ] Block the sender domain/address, URLs, and IPs at the email gateway and web proxy
- [ ] **Purge the email from all mailboxes** (M365: Content Search → Purge; Google: Investigation tool)
- [ ] If **credentials were entered**:
  - [ ] Reset the user's password and **revoke all active sessions/tokens**
  - [ ] Check for new MFA methods, inbox rules (forwarding!), and OAuth app grants added by the attacker
  - [ ] Review sign-in logs for logins from unusual IPs or countries
- [ ] If an **attachment was opened**: isolate the endpoint and switch to [Playbook 03: Malware](03-malware-endpoint.md)

## 4. Eradication & Recovery

- [ ] Confirm the email is gone from every mailbox
- [ ] Confirm the account is secure (MFA on, no malicious rules, no suspicious logins since the reset)
- [ ] Remove blocks only if the verdict was a false positive

## 5. Lessons Learned

- [ ] Write up the incident with the [incident report template](../templates/incident-report.md)
- [ ] Add new IOCs to the threat intel list / SIEM watchlist
- [ ] Thank the reporting user, since that reinforces good behavior
- [ ] If many users clicked, flag the campaign for security-awareness training

## Escalate to Tier 2 / IR lead when

- Credentials were entered **and** there are suspicious sign-ins afterward
- An attachment executed on a host
- An executive, finance, or IT admin account is involved
- More than 10 recipients clicked
