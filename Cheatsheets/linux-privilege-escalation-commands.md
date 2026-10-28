# 🐧 Linux Privilege Escalation — Command Cheatsheet

[← Cheatsheet index](README.md) · [Detailed study notes](../Topics-for-Studies/Linux-Privilege-Escalation.md) · [Resources](privilege-escalation-resources.md)

**Use:** authorized labs only. This is a quick reference; the topic pages explain why each command matters.

## Identify account, operating system and permissions

```bash
whoami
id
groups
hostname
uname -a
cat /etc/os-release
sudo -l
sudo -n -l
sudo --version | head -n 3
```

- `whoami`: current effective username.
- `id`: UID/GID/groups; check after switching accounts.
- `uname -a`: running kernel and architecture.
- `sudo -l`: allowed sudo commands; may require a password.
- `sudo -n -l`: does not prompt; reports an error if authentication is required.

## Inspect files and permissions

```bash
ls -lah
ls -la /home/user2
find / -perm -4000 -type f 2>/dev/null
find / -type f -writable 2>/dev/null
find / -type d -writable 2>/dev/null
find / -user user2 -type f 2>/dev/null
find /home /root -name ".bash_history" 2>/dev/null
find /home /root -type f -name ".*history" 2>/dev/null
find / -name "id_rsa" 2>/dev/null
getcap -r / 2>/dev/null
```

**Breakdown:** `find /` starts at root; `-perm -4000` matches the SUID bit; `-type f` regular files; `-type d` directories; `-writable` effective write access; `-user` owner; `-name` filename; `2>/dev/null` suppresses error output. SUID is not automatically a vulnerability.

### chmod — do not forget

```bash
chmod +x script.sh
chmod 755 script.sh
chmod 600 id_rsa
chmod 600 root_key
chmod u+s binary
```

| Mode | Owner | Group | Others |
|---|---|---|---|
| 600 | read/write | none | none |
| 644 | read/write | read | read |
| 755 | read/write/execute | read/execute | read/execute |

`chmod 600 root_key` protects the **local** key copy; it does not grant root privileges or repair the original exposed key. `chmod u+s` sets SUID and should only be used with authorization.

## Processes, scheduled tasks and packages

```bash
ps auxww
cat /etc/crontab
ls -la /etc/cron.d/
ls -la /var/spool/cron/crontabs/root
dpkg -l
```

`ps auxww` displays process owners and full command lines. Cron locations can expose task configuration; missing files or access denied are useful findings, not proof of a vulnerability.

## History and configuration checks

```bash
history
history 20
cat ~/.bash_history
less ~/.bash_history
ls -la ~ | grep bash_history
grep "ssh" ~/.bash_history
grep "sudo" ~/.bash_history
ls -la /var/www /opt /srv 2>/dev/null
ls -la /opt /var/backups /var/www /tmp 2>/dev/null
```

A history file may not exist or may not have been written yet. Keep passwords and secrets out of public reports.

## Sudo and SSH — syntax

```bash
sudo -u user2 -g user2 /bin/bash
su - username
sudo -u username -i
exit
ssh -i root_key -p PORT root@TARGET_IP
ssh -i root_key -o IdentitiesOnly=yes -p PORT root@TARGET_IP
ssh -v -i root_key -o IdentitiesOnly=yes -o PreferredAuthentications=publickey -p PORT root@TARGET_IP
ssh-keygen -y -f root_key > /dev/null
```

- `sudo -u` selects a user; `-g` selects a group. A sudo rule for one user does **not** imply root access.
- `ssh -i` selects the key file, `-p` a non-default port, and `-v` verbose debugging.
- `ssh-keygen -y -f` tests parsing and derives the public key from a private key. Do not publish key contents.

## Script-based enumeration

```bash
./linpeas.sh
```

Review scripts before running them; broad enumeration can be noisy. See [tools and resources](privilege-escalation-resources.md).

## Practical verification

```bash
whoami
id
```

Record actual output and whether the objective was met. Never claim success on a guessed path or expected result.
