# Alert Triage Checklist (any alert, under 10 minutes)

Use this for every alert before you pick a playbook.

## 1. Understand the alert
- [ ] What rule fired, and **what behavior is it designed to catch?**
- [ ] Which ATT&CK technique does it map to?
- [ ] When did it fire, and how often has this rule fired before? (Is it noisy?)

## 2. The 5 W's
- [ ] **Who:** user account(s). Privileged? Service account? VIP?
- [ ] **What:** process, file, command line, hash, email
- [ ] **Where:** host(s), source and destination IPs, internal or external
- [ ] **When:** first seen, last seen, during business hours?
- [ ] **Why it matters:** what could the attacker do next if this is real?

## 3. Quick enrichment
- [ ] IP / domain / hash reputation (VirusTotal, AbuseIPDB, URLScan)
- [ ] Is this normal for this user and host? (compare against the last 7-30 days)
- [ ] Any **related alerts** on the same user/host in the last 24 hours?

## 4. Decide
| Verdict | Action |
|---|---|
| **True positive** | Open an incident, follow the matching playbook, set severity |
| **Benign true positive** (real activity, authorized) | Document why, consider tuning the rule |
| **False positive** | Document why, submit a tuning request with evidence |
| **Not enough information** | Escalate to Tier 2 with everything gathered so far |

## 5. Document (always)
- [ ] Verdict, evidence, queries used, time spent
- [ ] Write it so another analyst could repeat your work without asking you
