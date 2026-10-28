# File Permissions, SUID, find & chmod — Detailed Notes

[← PrivEsc Topics](README.md) · [Command Cheatsheet](../../Cheatsheets/linux-privilege-escalation-commands.md)

## Understanding permission bits

For the permission string `-rw-------`, the first character identifies the file type (`-` regular file; `d` directory). The next three triplets specify owner, group, and other permissions. `r` is read, `w` is write and `x` is execute.

| Mode | Symbolic representation | Meaning |
|---|---|---|
| `600` | `rw-------` | Owner reads/writes; no group/other access |
| `644` | `rw-r--r--` | Owner reads/writes; group and others read |
| `755` | `rwxr-xr-x` | Owner has full access; group/others read and execute |

Directories require special attention: `x` allows directory traversal, while `r` allows listing names, subject to permission checks.

## Why `find` matters

```bash
find / -perm -4000 -type f 2>/dev/null
```

- `find`: search filesystem entries.
- `/`: start at the filesystem root.
- `-perm -4000`: match any regular file with the SUID bit set.
- `-type f`: only regular files, excluding directories.
- `2>/dev/null`: redirect stderr (file descriptor 2) to the null device, hiding permission errors from the display.

A SUID result is an enumeration finding, **not proof of a vulnerability**. Compare the actual program and configuration against what is expected.

Other useful checks:

```bash
find / -type f -writable 2>/dev/null
find / -type d -writable 2>/dev/null
find / -user user2 -type f 2>/dev/null
find /home /root -name ".bash_history" 2>/dev/null
find /home /root -type f -name ".*history" 2>/dev/null
find / -name "id_rsa" 2>/dev/null
```

`-writable` tests write access for the current user, `-user` selects the file owner, and `-name` matches entry names. Directory access may affect what can be found.

## Do not forget chmod

```bash
chmod +x script.sh
chmod 755 script.sh
chmod 600 id_rsa
chmod 600 root_key
chmod u+s binary
```

- `chmod +x`: grant executable permission as allowed by current mode, mask and ownership.
- `chmod 755`: owner can read/write/execute; group/others can read/execute.
- `chmod 600`: owner read/write, with no group/other access; appropriate for a local SSH private-key copy.
- `chmod u+s`: set SUID on a file, which changes execution behavior for supported executables; use only when explicitly authorized.

### Key-handling lesson

The lab produced `Load key "root_key": error in libcrypto` after the copied private key lacked its header and footer. Both `-----BEGIN ...-----` and `-----END ...-----`, all encoded lines and original line breaks are required. Correcting the text fixed the malformed-key problem.

After saving the copy, use `chmod 600 root_key` locally to limit access. This does not change root-owned key permissions on the target, nor grant root access by itself.

## Quick self-test

1. Why is `find / -perm -4000 -type f 2>/dev/null` useful?
2. What is the difference between `600` and `644`?
3. Why do directory execute permissions matter?
4. Why does `chmod 600 root_key` belong on the local file?
5. How does malformed private-key content differ from permission errors?
