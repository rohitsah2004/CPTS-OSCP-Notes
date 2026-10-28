# 🔎 Privilege Escalation — Tools & Resources

[← Cheatsheet index](README.md) · [Study Topics](../Topics-for-Studies/Linux-Privilege-Escalation/README.md)

Use these resources to research findings from **authorized** assessments. Check versions, scope, prerequisites, and side effects before testing a technique.

## Methodology and checklists

| Resource | Link | What to look for |
|---|---|---|
| HackTricks — Linux PrivEsc | https://book.hacktricks.wiki/en/linux-hardening/linux-privilege-escalation-checklist.html | Enumeration steps, permissions, sudo, services, credentials |
| HackTricks — Windows PrivEsc | https://book.hacktricks.wiki/en/windows-hardening/checklist-windows-privilege-escalation.html | Windows privileges, services, tokens and credentials |
| PayloadsAllTheThings | https://github.com/swisskyrepo/PayloadsAllTheThings | Broad security-testing methodology and checklists |
| PayloadsAllTheThings — Linux | https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md | Linux privilege escalation reference |
| PayloadsAllTheThings — Windows | https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md | Windows privilege escalation reference |
| GTFOBins | https://gtfobins.github.io/ | Security-relevant behaviors of Unix binaries |
| LOLBAS | https://lolbas-project.github.io/# | Security-relevant behaviors of Windows binaries/scripts |

## Enumeration scripts

| Tool | Link | Platform |
|---|---|---|
| LinEnum | https://github.com/rebootuser/LinEnum | Linux |
| linuxprivchecker | https://github.com/sleventyeleven/linuxprivchecker | Linux |
| LinPEAS / WinPEAS (PEASS) | https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite | Linux and Windows |
| Seatbelt | https://github.com/GhostPack/Seatbelt | Windows |
| JAWS | https://github.com/411Hall/JAWS | Windows |

## Vulnerability research

| Resource | Link |
|---|---|
| Dirty COW background and examples | https://github.com/dirtycow/dirtycow.github.io/wiki/PoCs |

**Important:** Automated scripts may trigger monitoring and generate a large volume of activity. A version match alone does not prove exploitability, and kernel tests can destabilize systems.

## Where to read more

- [Enumeration and vulnerability research](../Topics-for-Studies/Linux-Privilege-Escalation/01-enumeration-and-vulnerabilities.md)
- [Sudo and scheduled tasks](../Topics-for-Studies/Linux-Privilege-Escalation/02-sudo-and-scheduled-tasks.md)
- [Exposed credentials and SSH](../Topics-for-Studies/Linux-Privilege-Escalation/05-credentials-and-key-management.md)
- [Completed lab report](../Labs/001-linux-privesc-user1-user2-root.md)

## Direct reference links from study notes

| Topic | Resource | How to use it |
|---|---|---|
| Linux privilege escalation | [HackTricks Linux PrivEsc](https://hacktricks.wiki/linux-hardening/linux-basics/linux-privilege-escalation/index.html) | Consult the Linux privilege escalation methodology and enumeration checklist |
| Exploitation methodology | [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) | Cross-reference techniques, prerequisites and testing methods |
| Reverse shells | [InternalAllTheThings Reverse Shell Cheatsheet — Summary](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#summary) | Compare reverse-shell approaches for authorized lab environments |
| Bind shells | [InternalAllTheThings Bind Shell Cheatsheet](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-bind-cheatsheet/) | Compare bind-shell communication patterns |

These are external technical references, not attribution for the author of these personal study notes.
