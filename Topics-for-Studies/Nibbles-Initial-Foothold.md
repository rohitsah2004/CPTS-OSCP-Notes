# Nibbles — Initial Foothold (Guided Example)

**Status:** Walkthrough material, **not** marked as an independently completed lab.  
**Coverage:** Admin interface, file-upload behavior, code execution checks, reverse shell, and terminal upgrade.

[← Nibbles enumeration](Nibbles-Enumeration-and-Web-Footprinting.md) · [Commands](../Cheatsheets/nibbles-enumeration-commands.md)

## 1. Reasoning from the administrator interface

The example administrator portal contains Publish, Comments, Manage, Settings, Themes and Plugins. The Settings area indicates the installed application version is 4.0.3; the Plugins area includes a **My image** upload feature. A file upload handler accepting code in place of an image can create a security boundary failure when the server saves that code under a web-accessible executable filename.

The initial code-execution check is a short PHP snippet:

```php
<?php system('id'); ?>
```

The `system()` function executes a supplied shell command in the server process context. Here `id` is a diagnostic command that prints the UID, GID and group memberships. A correct response establishes the account used by the web process, not root access.

## 2. Upload behavior and server messages

The example image-processing code returned warnings involving `imagesx()`, `imagesy()`, `imagecreatetruecolor()`, `imagecopyresampled()`, `imagejpeg()` and `imagedestroy()`. This is compatible with the upload handling a file that is not a valid image. **Errors are not proof that the upload failed or succeeded.**

The example walkthrough then checked the web-accessible plugin upload location:

```text
/nibbleblog/content/private/plugins/my_image/
```

The example identifies `db.xml` and `image.php` in that folder. The web process response was demonstrated as:

```text
uid=1001(nibbler) gid=1001(nibbler) groups=1001(nibbler)
```

This means the PHP command was run as the application account in that example, not as root.

## 3. Reverse shell concept

The example proceeds by moving from individual HTTP-triggered commands to an interactive network session. The operator must determine the correct VPN interface address and select a reachable listening port. A shell established through a network client may offer limited job control and terminal behavior.

The example uses the following listener command:

```bash
nc -lvnp 9443
```

- `-l`: listen for an incoming connection.
- `-v`: verbose diagnostics.
- `-n`: do not perform DNS lookups.
- `-p 9443`: set listening port to 9443.

The illustrative connection output reports the remote host's connection and a limited shell environment (`can't access tty; job control turned off`). These values are examples from the supplied material, not a new result obtained here.

## 4. Terminal improvement and Python version checks

The initial shell session does not support all interactive features. In the supplied example:

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

failed because `python` was not installed under that name. The subsequent environment check showed:

```bash
which python3
```

Result:

```text
/usr/bin/python3
```

The study example then used the Python 3 interpreter to create a pseudoterminal:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

The account's home directory contained `personal.zip` and `user.txt`. The provided excerpt stops at this stage. No later privilege escalation or final flag completion is established by these notes.

## 5. Key learning points

1. Examine applications and plugin capabilities carefully.
2. Distinguish upload-processing errors from validation of an upload's actual outcome.
3. Confirm command execution by recording both the command and returned identity.
4. Understand why basic remote shells lack TTY capabilities.
5. Identify interpreter availability before assuming `python` exists.
6. Only mark the lab completed after verifying the actual objective.

## Additional references

- [Nibbleblog file-upload issue](https://www.rapid7.com/db/modules/exploit/multi/http/nibbleblog_file_upload/)
- [Reverse shell reference](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#summary)
