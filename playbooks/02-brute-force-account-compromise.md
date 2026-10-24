# Playbook 02: Brute Force / Suspected Account Compromise

| | |
|---|---|
| **Trigger** | SIEM alert for many failed logins from one source, password spraying across many accounts, or a successful login after failures |
| **ATT&CK** | T1110.001 Password Guessing · T1110.003 Password Spraying · T1078 Valid Accounts · T1021 Remote Services |
| **Severity guide** | Medium = failures only, blocked · High = spraying across many accounts · Critical = **successful login after failures** |
| **Tools** | [`ssh_bruteforce_detector.py`](https://github.com/kamronw23/security-scripts/tree/main/log-analysis), [`Get-FailedLogons.ps1`](https://github.com/kamronw23/security-scripts/tree/main/log-analysis), [SIEM searches](https://github.com/kamronw23/siem-detection-rules), AbuseIPDB, GeoIP lookup |

---

## 1. Triage

- [ ] Identify the **source IP(s)**, **target account(s)**, **target host(s)**, and the time window
- [ ] Is the source **internal or external**? Internal sources may be a compromised host or a misconfigured service.
- [ ] Run the matching detector on the logs:
  - Linux: `python3 ssh_bruteforce_detector.py /var/log/auth.log --threshold 5`
  - Windows: `.\Get-FailedLogons.ps1 -Hours 24 -Threshold 5`
- [ ] **The key question: did any attempt succeed?**
  - Linux: `Accepted password` / `Accepted publickey` from the same IP
  - Windows: Event **4624** from the same source after the 4625s

## 2. Analysis

| Pattern | Likely meaning |
|---|---|
| One IP, one account, many failures | Targeted password guessing |
| One IP, **many accounts**, few failures each | Password spraying |
| Many IPs, one account | Distributed attack (botnet) or account lockout DoS |
| Internal IP, one account, steady failures | Stale credentials in a service, mapped drive, or phone (likely benign) |

- [ ] Check the source IP's reputation (AbuseIPDB, VirusTotal) and geolocation
- [ ] For targeted accounts: are they real, privileged, disabled, or service accounts?
- [ ] If a login **succeeded**, review everything that account did afterward:
  - [ ] Processes launched, files accessed, new users or scheduled tasks created
  - [ ] Linux: `last`, `~/.bash_history`, `/var/log/auth.log` for `sudo`
  - [ ] Windows: 4688 process creation, 4720 user created, 4732 group change, 4698 task created

**Verdict:** ☐ Attack, blocked ☐ Attack, **succeeded** ☐ Benign (stale credentials / user error)

## 3. Containment

- [ ] Block the source IP(s) at the firewall / edge (or enable fail2ban on Linux hosts)
- [ ] If a login succeeded:
  - [ ] **Disable the account or reset its password immediately**, and revoke sessions and SSH keys
  - [ ] **Isolate the host** the attacker logged into
  - [ ] Look for persistence: new accounts, `authorized_keys` entries, cron jobs, scheduled tasks, services
- [ ] If spraying hit many accounts: force password resets for any account that might have been guessed

## 4. Eradication & Recovery

- [ ] Remove any persistence the attacker added
- [ ] Re-enable the account with a new password **and MFA**
- [ ] Harden the exposed service: SSH keys only, `PermitRootLogin no`, MFA on VPN/RDP, and keep RDP off the internet
- [ ] Run [`linux_audit.sh`](https://github.com/kamronw23/linux-hardening-audit) on affected Linux hosts

## 5. Lessons Learned

- [ ] Document the incident with the [incident report template](../templates/incident-report.md)
- [ ] Tune the detection threshold if it was too noisy or too slow
- [ ] Ask why the service was reachable from the internet in the first place

## Escalate to Tier 2 / IR lead when

- **Any successful login** from an attacking IP
- A privileged or admin account was targeted successfully
- Evidence of lateral movement to other hosts
