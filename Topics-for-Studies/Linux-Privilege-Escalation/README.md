# 🐧 Linux Privilege Escalation — Complete Study Guide

[← Topics](../README.md) · [← Home](../../README.md) · [Cheatsheet](../../Cheatsheets/linux-privilege-escalation-commands.md)

This is the topic-by-topic learning library. It includes detailed explanations, example outputs, technical commands, and direct reference links. The completed exercise is documented separately.

## Read in order

| Chapter | Concepts |
|---|---|
| [01 — Enumeration & vulnerabilities](01-enumeration-and-vulnerabilities.md) | Checklists, LinEnum, LinPEAS, Seatbelt, JAWS, kernel vulnerabilities, installed software |
| [02 — Sudo & scheduled tasks](02-sudo-and-scheduled-tasks.md) | `sudo -l`, `NOPASSWD`, user switching, GTFOBins, cron |
| [03 — SSH keys](03-ssh-keys.md) | Key files, permissions, `ssh-keygen`, key formatting and authentication |
| [04 — Credentials & SSH explanations](05-credentials-and-key-management.md) | Credential exposure, history, public/private key examples |
| [Command cheatsheet](../../Cheatsheets/linux-privilege-escalation-commands.md) | All core enumeration, `find`, `chmod`, sudo and SSH commands |
| [Resource directory](../../Cheatsheets/privilege-escalation-resources.md) | HackTricks, PayloadsAllTheThings, LinPEAS, GTFOBins, LOLBAS, and other tools |
| [Completed practical](../../Labs/001-linux-privesc-user1-user2-root.md) | Verified user1 → user2 → root path and troubleshooting |

## Suggested study sequence

1. Identify the current user and effective privileges.
2. Enumerate the OS, installed software, processes and relevant permissions.
3. Understand and interpret sudo rules and SUID findings.
4. Check cron, exposed credentials and SSH-key protections where authorized.
5. Practice `find`, `chmod`, `sudo -l`, and SSH diagnostics until you can explain every flag.
6. Recreate the successful evidence chain without copying the walkthrough.

**All passwords and private-key contents must stay out of GitHub.**
