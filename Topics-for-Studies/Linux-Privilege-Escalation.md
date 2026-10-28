# Linux Privilege Escalation — Master Study Notes

**All topic explanations in one document.**

[← Topics](../README.md) · [Completed Lab](../../Labs/001-linux-privesc-user1-user2-root.md) · [Commands](../../Cheatsheets/linux-privilege-escalation-commands.md) · [Resources](../../Cheatsheets/privilege-escalation-resources.md)

The sections below retain the detailed technical explanations, reference links, example outputs and commands for studying this topic. Examples are not necessarily results from the completed lab.

## PrivEsc Checklists

Once we gain initial access to a box, we want to thoroughly enumerate the box to find any potential vulnerabilities we can exploit to achieve a higher privilege level. We can find many checklists and cheat sheets online that have a collection of checks we can run and the commands to run these checks. One excellent resource is [HackTricks](https://book.hacktricks.xyz/), which has an excellent checklist for both [Linux](https://book.hacktricks.wiki/en/linux-hardening/linux-privilege-escalation-checklist.html) and [Windows](https://book.hacktricks.wiki/en/windows-hardening/checklist-windows-privilege-escalation.html) local privilege escalation. Another excellent repository is [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings), which also has checklists for both [Linux](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md) and [Windows](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md). We must start experimenting with various commands and techniques and get familiar with them to understand multiple weaknesses that can lead to escalating our privileges.

---

## Enumeration Scripts

Many of the above commands may be automatically run with a script to go through the report and look for any weaknesses. We can run many scripts to automatically enumerate the server by running common commands that return any interesting findings. Some of the common Linux enumeration scripts include [LinEnum](https://github.com/rebootuser/LinEnum.git) and [linuxprivchecker](https://github.com/sleventyeleven/linuxprivchecker), and for Windows include [Seatbelt](https://github.com/GhostPack/Seatbelt) and [JAWS](https://github.com/411Hall/JAWS).

Another useful tool we may use for server enumeration is the [Privilege Escalation Awesome Scripts SUITE (PEASS)](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite), as it is well maintained to remain up to date and includes scripts for enumerating both Linux and Windows.

Note: These scripts will run many commands known for identifying vulnerabilities and create a lot of "noise" that may trigger anti-virus software or security monitoring software that looks for these types of events. This may prevent the scripts from running or even trigger an alarm that the system has been compromised. In some instances, we may want to do a manual enumeration instead of running scripts.

Let us take an example of running the Linux script from `PEASS` called `LinPEAS`:

```
        shellsession
$ ./linpeas.sh
...SNIP...

Linux Privesc Checklist: https://book.hacktricks.xyz/linux-unix/linux-privilege-escalation-checklist
 LEYEND:
  RED/YELLOW: 99% a PE vector
  RED: You must take a look at it
  LightCyan: Users with console
  Blue: Users without console & mounted devs
  Green: Common things (users, groups, SUID/SGID, mounts, .sh scripts, cronjobs)
  LightMangenta: Your username


====================================( Basic information )=====================================
OS: Linux version 3.9.0-73-generic
User & Groups: uid=33(www-data) gid=33(www-data) groups=33(www-data)
...SNIP...


```

As we can see, once the script runs, it starts collecting information and displaying it in an excellent report. Let us discuss some of the vulnerabilities that we should look for in the output from these scripts.

---

## Kernel Exploits

Whenever we encounter a server running an old operating system, we should start by looking for potential kernel vulnerabilities that may exist. Suppose the server is not being maintained with the latest updates and patches. In that case, it is likely vulnerable to specific kernel exploits found on unpatched versions of Linux and Windows.

For example, the above script showed us the Linux version to be `3.9.0-73-generic`. If we Google exploits for this version or use `searchsploit`, we would find a `CVE-2016-5195`, otherwise known as `DirtyCow`. We can search for and download the [DirtyCow](https://github.com/dirtycow/dirtycow.github.io/wiki/PoCs) exploit and run it on the server to gain root access.

The same concept also applies to Windows, as there are many vulnerabilities in unpatched/older versions of Windows, with various vulnerabilities that can be used for privilege escalation. We should keep in mind that kernel exploits can cause system instability, and we should take great care before running them on production systems. It is best to try them in a lab environment and only run them on production systems with explicit approval and coordination with our client.

---

## Vulnerable Software

Another thing we should look for is installed software. For example, we can use the `dpkg -l` command on Linux or look at `C:\Program Files` in Windows to see what software is installed on the system. We should look for public exploits for any installed software, especially if any older versions are in use, containing unpatched vulnerabilities.

---

---

## User Privileges

Another critical aspect to look for after gaining access to a server is the privileges available to the user we have access to. Suppose we are allowed to run specific commands as root (or as another user). In that case, we may be able to escalate our privileges to root/system users or gain access as a different user. Below are some common ways to exploit certain user privileges:

1. Sudo
2. SUID
3. Windows Token Privileges

The `sudo` command in Linux allows a user to execute commands as a different user. It is usually used to allow lower privileged users to execute commands as root without giving them access to the root user. This is generally done as specific commands can only be run as root 'like `tcpdump`' or allow the user to access certain root-only directories. We can check what `sudo` privileges we have with the `sudo -l` command:

```
        shellsession
$ sudo -l

[sudo] password for user1:
...SNIP...

User user1 may run the following commands on ExampleServer:
    (ALL : ALL) ALL


```

The above output says that we can run all commands with `sudo`, which gives us complete access, and we can use the `su` command with `sudo` to switch to the root user:

```
        shellsession
$ sudo su -

[sudo] password for user1:
whoami
root


```

The above command requires a password to run any commands with `sudo`. There are certain occasions where we may be allowed to execute certain applications, or all applications, without having to provide a password:

```
        shellsession
$ sudo -l

    (user : user) NOPASSWD: /bin/echo


```

The `NOPASSWD` entry shows that the `/bin/echo` command can be executed without a password. This would be useful if we gained access to the server through a vulnerability and did not have the user's password. As it says `user`, we can run `sudo` as that user and not as root. To do so, we can specify the user with `-u user`:

```
        shellsession
$ sudo -u user /bin/echo Hello World!

    Hello World!


```

Once we find a particular application we can run with `sudo`, we can look for ways to exploit it to get a shell as the root user. [GTFOBins](https://gtfobins.github.io/) contains a list of commands and how they can be exploited through `sudo`. We can search for the application we have `sudo` privilege over, and if it exists, it may tell us the exact command we should execute to gain root access using the `sudo` privilege we have.

[LOLBAS](https://lolbas-project.github.io/#) also contains a list of Windows applications which we may be able to leverage to perform certain functions, like downloading files or executing commands in the context of a privileged user.

---

## Scheduled Tasks

In both Linux and Windows, there are methods to have scripts run at specific intervals to carry out a task. Some examples are having an anti-virus scan running every hour or a backup script that runs every 30 minutes. There are usually two ways to take advantage of scheduled tasks (Windows) or cron jobs (Linux) to escalate our privileges:

1. Add new scheduled tasks/cron jobs
2. Trick them to execute a malicious software

The easiest way is to check if we are allowed to add new scheduled tasks. In Linux, a common form of maintaining scheduled tasks is through `Cron Jobs`. There are specific directories that we may be able to utilize to add new cron jobs if we have the `write` permissions over them. These include:

1. `/etc/crontab`
2. `/etc/cron.d`
3. `/var/spool/cron/crontabs/root`

If we can write to a directory called by a cron job, we can write a bash script with a reverse shell command, which should send us a reverse shell when executed.

---

---

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

---

Examples in this page are illustrative, not credentials from an actual system. Never publish real passwords or private keys.

## Exposed Credentials

Next, we can look for files we can read and see if they contain any exposed credentials. This is very common with `configuration` files, `log` files, and user history files (`bash_history` in Linux and `PSReadLine` in Windows). The enumeration scripts we discussed at the beginning usually look for potential passwords in files and provide them to us, as below:

```
        shellsession
...SNIP...
[+] Searching passwords in config PHP files
[+] Finding passwords inside logs (limit 70)
...SNIP...
/var/www/html/config.php: $conn = new mysqli(localhost, 'db_user', 'password123');


```

As we can see, the database password '`password123`' is exposed, which would allow us to log in to the local `mysql` databases and look for interesting information. We may also check for `Password Reuse`, as the system user may have used their password for the databases, which may allow us to use the same password to switch to that user, as follows:

```
        shellsession
$ su -

Password: password123
whoami

root


```

We may also use the user credentials to `ssh` into the server as that user.

---

## SSH Keys

Finally, let us discuss SSH keys. If we have read access over the `.ssh` directory for a specific user, we may read their private ssh keys found in `/home/user/.ssh/id_rsa` or `/root/.ssh/id_rsa`, and use it to log in to the server. If we can read the `/root/.ssh/` directory and can read the `id_rsa` file, we can copy it to our machine and use the `-i` flag to log in with it:

```
        shellsession
$ vim id_rsa
$ chmod 600 id_rsa
$ ssh root@10.10.10.10 -i id_rsa

root@10.10.10.10#


```

Note that we used the command 'chmod 600 id_rsa' on the key after we created it on our machine to change the file's permissions to be more restrictive. If ssh keys have lax permissions, i.e., maybe read by other people, the ssh server would prevent them from working.

If we find ourselves with write access to a users`/.ssh/` directory, we can place our public key in the user's ssh directory at `/home/user/.ssh/authorized_keys`. This technique is usually used to gain ssh access after gaining a shell as that user. The current SSH configuration will not accept keys written by other users, so it will only work if we have already gained control over that user. We must first create a new key with `ssh-keygen` and the `-f` flag to specify the output file:

```
        shellsession
$ ssh-keygen -f key

Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): *******
Enter same passphrase again: *******

Your identification has been saved in key
Your public key has been saved in key.pub
The key fingerprint is:
SHA256:...SNIP... user@parrot
The key's randomart image is:
+---[RSA 3072]----+
|   ..o.++.+      |
...SNIP...
|     . ..oo+.    |
+----[SHA256]-----+


```

This will give us two files: `key` (which we will use with `ssh -i`) and `key.pub`, which we will copy to the remote machine. Let us copy `key.pub`, then on the remote machine, we will add it into `/root/.ssh/authorized_keys`:

```
        shellsession
user@remotehost$ echo "ssh-rsa AAAAB...SNIP...M= user@parrot" >> /root/.ssh/authorized_keys


```

Now, the remote server should allow us to log in as that user by using our private key:

```
        shellsession
$ ssh root@10.10.10.10 -i key

root@remotehost#


```

As we can see, we can now ssh in as the user `root`.

---

## 9. SSH keys and authorized keys

### Key files

| File                         | Purpose                                  | Protect?                      |
| ---------------------------- | ---------------------------------------- | ----------------------------- |
| `/root/.ssh/id_rsa`          | Root's private SSH key, if one exists    | **Yes, secret**               |
| `/root/.ssh/id_rsa.pub`      | Corresponding public key                 | Public                        |
| `/root/.ssh/authorized_keys` | Public keys authorized to log in as root | Integrity-critical            |
| `~/.ssh/known_hosts`         | Saved server host keys                   | Helps detect identity changes |

Read access to a private key can enable SSH authentication **only if** its matching public key is authorized, the server permits the login, and any key passphrase requirements are satisfied.

### Inspect availability and permissions

```
ls -la /home/user1/.ssh
ls -la /home/user2/.ssh
ls -la /root/.ssh
find /home /root -name "id_rsa" 2>/dev/null
```

Actual lab output:

```
ls: cannot access '/home/user1/.ssh': No such file or directory
ls: cannot access '/home/user2/.ssh': No such file or directory

total 12
drwxr-x---. 1 root user2   20 Feb 12  2021 .
drwxr-x---. 1 root user2   18 Feb 12  2021 ..
-rw-------. 1 root root   571 Feb 12  2021 authorized_keys
-rw-r--r--. 1 root root  2602 Feb 12  2021 id_rsa
-rw-r--r--. 1 root root   571 Feb 12  2021 id_rsa.pub
```

**Why it mattered:** `/root/.ssh` granted traversal to group `user2` (`r-x`), and `id_rsa` was world-readable (`644`). Thus `user2` could read root's private key even though the directory and file were root-owned.

### Using an already accessible key in an authorized lab

On the target as `user2`:

```
cat /root/.ssh/id_rsa
# Or copy to a file inside the authorized lab environment:
cp /root/.ssh/id_rsa /home/user2/root_key
```

On the attacking machine, save an exact copy of the private key securely. If using Vim (no nano):

```
vim root_key
# Press i to insert; paste entire private key, including header and footer.
# Press Esc, then :wq and Enter.
chmod 600 root_key
ls -l root_key
ssh-keygen -y -f root_key > /dev/null  # validate parse/key; do not print key here
```

An alternative with no editor:

```
cat > root_key <<'EOF_KEY'
-----BEGIN RSA PRIVATE KEY-----
[entire private key contents go here]
-----END RSA PRIVATE KEY-----
EOF_KEY
chmod 600 root_key
```

**Important:** The displayed header/footer are illustrative; use the *actual* header/footer (RSA or OPENSSH) from the real key. Retain every line and newline. Never paste key contents into a chat, public repo, or report.

Connection syntax:

```
ssh -i root_key -p PORT root@TARGET_IP
ssh -i root_key -o IdentitiesOnly=yes -p PORT root@TARGET_IP
ssh -v -i root_key -o IdentitiesOnly=yes -o PreferredAuthentications=publickey -p PORT root@TARGET_IP
```

Flags: `-i` identity/private-key file; `-p` custom SSH port; `-v` debug; `IdentitiesOnly=yes` restricts identity selection; `PreferredAuthentications=publickey` prioritizes key auth.

### Alternative: creating your own key pair (conceptual teaching example)

```
ssh-keygen -f key
# Creates private key 'key' and public key 'key.pub'
```

Public keys can be listed in an account's `~/.ssh/authorized_keys` **by an authorized administrator or the account owner**. Example syntax shown in the reference notes:

```
echo "ssh-rsa AAAAB...SNIP...M= user@parrot" >> /root/.ssh/authorized_keys
ssh root@10.10.10.10 -i key
```

That example only applies where the operator already has legitimate write permission to root's `authorized_keys`, and the SSH server accepts the key. **It was not the method used in the completed lab**: the completed lab used the **existing readable root private key**.

---