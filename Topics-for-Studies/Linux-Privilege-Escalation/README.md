# 🐧 Linux Privilege Escalation

[← All study topics](../README.md) · [← Home](../../README.md)

A topic-focused guide to local privilege escalation for CPTS and OSCP preparation.

## Topic map

| Area | What to learn | Reference |
|---|---|---|
| Identity and system | `whoami`, `id`, `groups`, `hostname`, `uname -a` | [Completed exercise](../../Labs/001-linux-privesc-user1-user2-root.md) |
| Sudo and user switching | Sudo rules, passwords, user/group scope | [Completed exercise](../../Labs/001-linux-privesc-user1-user2-root.md) |
| File permissions | `rwx`, numeric modes, SUID, `chmod` | [Completed exercise](../../Labs/001-linux-privesc-user1-user2-root.md) |
| Filesystem enumeration | `find`, writable locations, SUID searches | [Completed exercise](../../Labs/001-linux-privesc-user1-user2-root.md) |
| Processes and scheduled tasks | Running processes and cron | [Completed exercise](../../Labs/001-linux-privesc-user1-user2-root.md) |
| Credentials and shell history | Where sensitive information may appear | [Completed exercise](../../Labs/001-linux-privesc-user1-user2-root.md) |
| SSH keys | Key files, permissions and safe handling | [Detailed SSH study notes](03-ssh-keys.md) |

## Essential principles

- Distinguish **cross-user access** from **root privilege escalation**.
- Verify privilege changes with identity checks rather than assuming success.
- A file owned by root is not necessarily unreadable by other users.
- A SUID file is not automatically exploitable.
- A sudo permission for one account does not automatically apply to another.
- Private keys require strict local protection.
- Treat automated enumeration output as leads to verify, not proof.

**Note:** This is a navigational study overview. The accompanying completed exercise and SSH study file preserve detailed observed evidence. Some longer topic uploads remain pending.
