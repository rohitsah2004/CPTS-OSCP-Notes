# Nibbles — Reconnaissance and Web Footprinting Cheatsheet

[← Cheatsheets](README.md) · [Full enumeration notes](../Topics-for-Studies/Nibbles-Enumeration-and-Web-Footprinting.md) · [Foothold reference](../Topics-for-Studies/Nibbles-Initial-Foothold.md)

## Nmap reconnaissance

```bash
nmap -v -oG -
nmap -sV --open -oA nibbles_initial_scan 10.129.42.190
nmap -p- --open -oA nibbles_full_tcp_scan 10.129.42.190
nmap -sC -p 22,80 -oA nibbles_script_scan 10.129.42.190
nmap -sV --script=http-enum -oA nibbles_nmap_http_enum 10.129.42.190
```

| Option | What it does |
|---|---|
| `-v` | Verbose output |
| `-oG -` | Print greppable output to stdout (the example intentionally specifies no target to see default port list) |
| `-sV` | Detect service versions |
| `--open` | Show open ports |
| `-oA` | Write normal, XML, and greppable formats with a common basename |
| `-p-` | Scan all 65,535 TCP ports |
| `-sC` | Run Nmap's default NSE scripts |
| `-p 22,80` | Limit scan to SSH and HTTP ports |
| `--script=http-enum` | Run the http-enum script |

Example findings in the reference: SSH on port 22 and HTTP on port 80. The full-port scan added no new service in the supplied output.

## Banner grabbing

```bash
nc -nv 10.129.42.190 22
nc -nv 10.129.42.190 80
```

`-n` disables DNS resolution; `-v` provides verbose output. SSH may return a banner; the web service may not.

## Identify web technologies

```bash
whatweb 10.129.42.190
curl http://10.129.42.190
whatweb http://10.129.42.190/nibbleblog
curl http://10.129.42.190/nibbleblog/README
```

In the reference, an HTML comment exposed the `/nibbleblog/` path and its README identified version 4.0.3.

## Directory and content discovery

```bash
gobuster dir -u http://10.129.42.190/nibbleblog/ --wordlist /usr/share/seclists/Discovery/Web-Content/common.txt
gobuster dir -u http://10.129.42.190/ --wordlist /usr/share/seclists/Discovery/Web-Content/common.txt
```

`dir` selects directory enumeration; `-u` sets the target URL; `--wordlist` selects candidate path names. Common HTTP responses include `200` (response served), `301` (permanent redirect) and `403` (forbidden).

## Read XML with formatting

```bash
curl -s http://10.129.42.190/nibbleblog/content/private/users.xml | xmllint --format -
curl -s http://10.129.42.190/nibbleblog/content/private/config.xml | xmllint --format -
```

`curl -s` disables the progress meter; `|` passes the retrieved XML as stdin; `xmllint --format -` pretty-prints the XML. The reference output includes evidence of an administrator account, blacklist controls and application metadata.

## TTY and interpreter checks

```bash
which python3
python -c 'import pty; pty.spawn("/bin/bash")'
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

In the provided example, `python` was missing but `python3` existed. Check actual interpreter availability.

## Resources

- [Gobuster](https://github.com/OJ/gobuster)
- [Nmap NSE documentation](https://nmap.org/book/man-nse.html)
- [Nmap default scripts](https://nmap.org/nsedoc/categories/default.html)
- [Nmap http-enum](https://nmap.org/nsedoc/scripts/http-enum.html)
- [xmllint](https://linux.die.net/man/1/xmllint)
- [Nibbleblog vulnerability reference](https://www.rapid7.com/db/modules/exploit/multi/http/nibbleblog_file_upload/)
