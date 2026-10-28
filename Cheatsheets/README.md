# 🧰 Command Index

[← Home](../README.md) · [Labs](../INDEX.md)

Find a command by tool or task. Detailed commands from the original lab are preserved in their full writeups.

| Task | Tool/command | Reference |
|---|---|---|
| Detect services | `nmap -Pn -sC -sV` | [Web notes](web-and-public-exploits.md#enumeration) |
| Scan WordPress | `msfconsole`, `wordpress_scanner` | [Web notes](web-and-public-exploits.md#metasploit-wordpress-scanner) |
| Enumerate plugins | `wpscan -e ap` | [Web notes](web-and-public-exploits.md#enumeration) |
| Search public exploits | `searchsploit` | [Web notes](web-and-public-exploits.md#public-exploit-research) |
| Send GET parameters | `curl -G --data-urlencode` | [Web notes](web-and-public-exploits.md#http-basics) |
| Save HTTP response | `curl -o` | [Web notes](web-and-public-exploits.md#http-basics) |
| Print response headers | `curl -i` | [Lab 001](../CPTS/HTB-Academy/Public-Exploits/001-wordpress-simple-backup-path-traversal.md#5-verifying-the-result) |
| Read a response file | `cat` | [Lab 001](../CPTS/HTB-Academy/Public-Exploits/001-wordpress-simple-backup-path-traversal.md#5-verifying-the-result) |

**Reminder:** Copy from the full lab page when exact parameter values matter. Authorized lab systems only.

## Linux privilege escalation

- [Full Linux PrivEsc command reference](linux-privilege-escalation-commands.md) — `find`, `chmod`, sudo, SSH, enumeration, cron, permissions
- [Privilege escalation tools and resources](privilege-escalation-resources.md) — checklists, LinPEAS, LinEnum, Seatbelt, JAWS, GTFOBins and LOLBAS
- [Detailed topic chapters](../Topics-for-Studies/Linux-Privilege-Escalation/README.md)

## Quick links — external references

- [HackTricks — Linux Privilege Escalation](https://hacktricks.wiki/linux-hardening/linux-basics/linux-privilege-escalation/index.html)
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
- [Reverse Shell Cheatsheet](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#summary)
- [Full privilege escalation resource directory](privilege-escalation-resources.md)
