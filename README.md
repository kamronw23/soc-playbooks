# 📘 soc-playbooks

Incident response **playbooks**, an **alert triage checklist**, and an **incident report template** for the incidents a Tier 1 SOC analyst handles most often.

They follow the **NIST SP 800-61** incident response lifecycle (*Preparation → Detection & Analysis → Containment, Eradication & Recovery → Post-Incident Activity*) and map each incident type to **MITRE ATT&CK**. Each playbook links to the tools and detections I built, so this repo ties the rest of my portfolio together.

---

## 📂 Contents

| File | When to use it |
|---|---|
| [`templates/alert-triage-checklist.md`](templates/alert-triage-checklist.md) | **Every alert.** A 10-minute triage covering the 5 W's, enrichment, and a verdict |
| [`playbooks/01-phishing.md`](playbooks/01-phishing.md) | A user reports a suspicious email, or the gateway flags one |
| [`playbooks/02-brute-force-account-compromise.md`](playbooks/02-brute-force-account-compromise.md) | Failed-login spikes, password spraying, or a success after failures |
| [`playbooks/03-malware-endpoint.md`](playbooks/03-malware-endpoint.md) | EDR/AV alert, suspicious process, possible ransomware |
| [`templates/incident-report.md`](templates/incident-report.md) | Writing up any confirmed incident |

## 🔄 How It Fits Together

```
 Alert fires (siem-detection-rules)
        │
        ▼
 Alert triage checklist ──► False positive? ──► document + tune rule
        │
        ▼ true positive
 Pick a playbook (01 phishing · 02 brute force · 03 malware)
        │   uses tools from security-scripts / linux-hardening-audit
        ▼
 Contain → Eradicate → Recover
        │
        ▼
 Incident report + lessons learned ──► new / better detection rules
```

## 🧩 Each Playbook Includes

- **Trigger**, **ATT&CK mapping**, and a **severity guide**
- Step-by-step checklists for **Triage → Analysis → Containment → Eradication & Recovery → Lessons Learned**
- A **verdict** step, so every investigation ends in a decision
- Clear **escalation criteria** for when Tier 1 should hand off to Tier 2

## 🗺️ Roadmap

- [ ] Playbook 04: Suspicious login (impossible travel / new country / MFA fatigue)
- [ ] Playbook 05: Data exfiltration / unusual outbound traffic
- [ ] Playbook 06: Insider threat / unauthorized access
- [ ] A worked example incident report from my home lab

---

Part of my [cybersecurity portfolio](https://github.com/kamronw23/Portfolio) · Related: [security-scripts](https://github.com/kamronw23/security-scripts) · [siem-detection-rules](https://github.com/kamronw23/siem-detection-rules) · [linux-hardening-audit](https://github.com/kamronw23/linux-hardening-audit)
