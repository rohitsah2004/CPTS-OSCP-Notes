# 🔐 CPTS & OSCP — Penetration Testing Study Notes

**A searchable, practical knowledge base for HTB Academy CPTS and OffSec OSCP preparation.**

![CPTS](https://img.shields.io/badge/Track-CPTS-1679A7) ![OSCP](https://img.shields.io/badge/Track-OSCP-444444) ![Notes](https://img.shields.io/badge/Labs_Documented-1-blue) ![Lab%20Status](https://img.shields.io/badge/Latest_Lab-Verification_Pending-orange)

> **Purpose:** Record full commands, flags, payloads, errors, explanations, output, and lessons learned. A lab is not marked successful until its objective has been verified. All testing must be authorized.

## 🧭 Quick navigation

| I want to… | Go here |
| --- | --- |
| Browse every lab | [Lab index](INDEX.md) |
| Find a command quickly | [Command index](Cheatsheets/README.md) |
| Review WordPress plugin exploitation | [Lab 001 — Simple Backup](CPTS/HTB-Academy/Public-Exploits/001-wordpress-simple-backup-path-traversal.md) |
| Practice web requests and SearchSploit | [Web exploitation cheatsheet](Cheatsheets/web-and-public-exploits.md) |
| Review CPTS Academy notes | [CPTS folder](CPTS/README.md) |
| Review OSCP methodology | [OSCP folder](OSCP/README.md) |
| Document a new lab | [Reusable lab template](Templates/lab-template.md) |
| Track exam readiness | [Study tracker](STUDY-TRACKER.md) |

## 🧪 Practical labs

- [Browse both practical exercises](Labs/README.md)
- [Lab 001 — Linux Privilege Escalation (completed)](Labs/001-linux-privesc-user1-user2-root.md)
- [Lab 002 — WordPress Simple Backup (attempted, unverified)](Labs/002-wordpress-simple-backup.md)

## 📖 Study topics

- [Types of Shells — reverse, bind and web shells](Topics-for-Studies/Types-of-Shells.md)
- [Browse all study topics](Topics-for-Studies/README.md)
- [Linux Privilege Escalation — master study guide](Topics-for-Studies/Linux-Privilege-Escalation.md)

## 📚 Current lab library

| # | Topic | Techniques | Verification |
| --- | --- | --- | --- |
| [001](CPTS/HTB-Academy/Public-Exploits/001-wordpress-simple-backup-path-traversal.md) | WordPress Simple Backup 2.7.10 | Nmap, Metasploit, SearchSploit, curl, path traversal | ⏳ Not confirmed |

## 🎯 Study method

1. **Enumerate** — identify ports, services, versions, and technologies.
2. **Research** — verify vulnerabilities, versions, and prerequisites.
3. **Test** — record exact commands and responses within the permitted lab.
4. **Validate** — check evidence instead of relying on an HTTP status alone.
5. **Explain** — understand *why* the technique works.
6. **Review** — turn discoveries into quick-reference commands and practice again.

## 🗂️ Repository layout

```text
CPTS-OSCP-Notes/
├── README.md
├── INDEX.md
├── STUDY-TRACKER.md
├── CPTS/
│   ├── README.md
│   └── HTB-Academy/Public-Exploits/
│       └── 001-wordpress-simple-backup-path-traversal.md
├── OSCP/
│   └── README.md
├── Cheatsheets/
│   ├── README.md
│   └── web-and-public-exploits.md
└── Templates/
    └── lab-template.md
```

## ✅ Documentation standard

- Preserve the **exact original commands**, with every flag, parameter, URL, and path.
- Give a command-by-command breakdown, prerequisites, and expected behavior.
- Record **errors, troubleshooting, output, and actual verification status**.
- Clearly separate observed results from hypotheses.
- Link each lab to relevant cheatsheets and concepts.
- Never add credentials, API keys, or confidential target information.

### Finding material fast

Use GitHub's **Go to file** (`t`) to search filenames, or the repository search to find commands such as `curl`, `nmap`, `searchsploit`, and `../`.

> ⚠️ **Visibility:** This repository was public when initially checked. Keep sensitive data out of commits; switch to private in repository settings if you prefer.
