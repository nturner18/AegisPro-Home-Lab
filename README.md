# AegisPro Home Lab
## Ongoing Security Practice & Advanced Lab Work
### AegisPro CyberShield TX

> A living repository documenting ongoing cybersecurity practice, advanced tooling integration, attack simulation, detection engineering, and remediation work. This repo picks up where **[Aegispro-Lab-Setup](https://github.com/nturner18/Aegispro-Lab-Setup)** left off.

**Operator:** Nick Turner, CISSP | AegisPro CyberShield TX
**Website:** [aegisprotx.com](https://www.aegisprotx.com)
**Location:** Fort Worth, Texas

> **Disclaimer:** All testing performed exclusively against owned, intentionally vulnerable machines on an isolated private network. No unauthorized systems are ever targeted.

---

## Lab Environment

| Component | Details |
|-----------|---------|
| Host Machine | Custom PC — Intel i9-10900KF, 64GB DDR4, MSI MPG Z590 Gaming Carbon WiFi |
| Hypervisor | VMware Workstation Pro 25H2 |
| Attacker | Kali Linux 2025.2 — 10.0.1.10 |
| Firewall / Router | pfSense 2.8.1 — 10.0.1.1 |
| Domain Controller | ASBankDC1 — Windows Server 2019 — 10.0.1.201 (ASBank.com) |
| Workstations | ASB-CB-WS01, ASB-PB-WS02 (Windows 10 Pro) |
| SIEM / NSM | Security Onion 2.x (Evaluation mode) — 24GB RAM |
| Linux Target | Metasploitable 2 — 10.0.1.104 |
| Web App Target | DVWA — 10.0.1.101 |

For full lab build documentation see **[Aegispro-Lab-Setup](https://github.com/nturner18/Aegispro-Lab-Setup)**.

---

## Repository Structure

```
AegisPro-Home-Lab/
├── README.md                          ← this file
├── labs/
│   ├── security-onion-setup/          ← SO deployment and validation
│   │   ├── README.md
│   │   └── images/
│   ├── attack-and-detect/             ← attack simulation with SO monitoring
│   │   ├── metasploitable-detection/  ← vsftpd exploit detection
│   │   │   ├── README.md
│   │   │   └── images/
│   │   └── ad-attack-detection/       ← Phase 9 attack chain detection
│   │       ├── README.md
│   │       └── images/
│   └── ad-remediation/                ← fix AD misconfigs from Phase 9
│       ├── README.md
│       └── images/
├── tools/                             ← tool configs, scripts, custom rules
└── notes/                             ← reference notes and research
```

---

## Labs

| Lab | Description | Status |
|-----|-------------|--------|
| [Security Onion Setup](./labs/security-onion-setup/README.md) | Deploy SO, configure monitoring interface, validate traffic capture | 🔄 In Progress |
| [Attack & Detect — Metasploitable](./labs/attack-and-detect/metasploitable-detection/README.md) | Run vsftpd exploit, document SO detection | ⏳ Pending |
| [Attack & Detect — AD Attack Chain](./labs/attack-and-detect/ad-attack-detection/README.md) | Rerun Phase 9 attacks, map detections to MITRE ATT&CK | ⏳ Pending |
| [AD Remediation](./labs/ad-remediation/README.md) | Fix AD misconfigurations, validate controls, retest attacks | ⏳ Pending |

---

## The Security Operations Cycle

This repo follows the full defensive security lifecycle:

```
Attack Simulation     → understand how attacks work
Detection Engineering → identify what evidence they leave
Remediation           → fix the underlying weaknesses
Validation            → confirm controls are effective
```

This mirrors the methodology AegisPro CyberShield TX applies in real client engagements — identify, detect, remediate, validate.

---

## CISSP Domain Alignment

| Domain | Lab Activities |
|--------|---------------|
| Domain 4 — Communication & Network Security | Network traffic analysis, SO monitoring interface configuration |
| Domain 6 — Security Assessment & Testing | Attack simulation, detection validation, remediation testing |
| Domain 7 — Security Operations | SIEM operation, alert triage, event correlation, detection engineering |

---

## Related Repositories

| Repo | Description |
|------|-------------|
| [Aegispro-Lab-Setup](https://github.com/nturner18/Aegispro-Lab-Setup) | Complete 10-phase lab build documentation |
| [Aegispro-Security-Assessments](https://github.com/nturner18/Aegispro-Security-Assessments) | Real client engagement documentation |

---

*AegisPro CyberShield TX | www.aegisprotx.com | nturner@aegisprotx.com*
*Last updated: June 2026*
