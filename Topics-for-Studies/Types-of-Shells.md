# Types of Shells — CPTS & OSCP Study Topic

**Category:** Topics for Studies → Shells and Remote Access  
**Type:** Study notes, not a completed lab

[← Home](../README.md) · [Topics index](README.md)

## Overview

Shells enable command-line access to systems in an authorized penetration-testing environment. The three primary types are **reverse**, **bind**, and **web** shells.

| Type | How it communicates | Important consideration |
| --- | --- | --- |
| Reverse shell | Target connects outward toward a listener | Outbound network reachability |
| Bind shell | Target listens for an inbound connection | Inbound firewall and network restrictions |
| Web shell | Commands and outputs pass through a web application | HTTP transport, limited interactivity |

## Network and identity diagnostics

```bash
ip a
id
echo $TERM
stty size
```

- `ip a` — show network interfaces and addresses, including a VPN tunnel such as `tun0`.
- `id` — show the account's UID, GID, and groups.
- `echo $TERM` — print the terminal type.
- `stty size` — display terminal rows and columns.

## Shell concepts to review

### Reverse shell

A reverse shell initiates an outbound connection from a target system toward a listening system. Study the purpose of a listener, port choice, routing through lab VPN interfaces, and why a lost session may require re-establishing access.

### Bind shell

A bind shell exposes a listening service on the target for incoming connections. Review network restrictions, listener lifetime, and the distinction between an interrupted client connection and a stopped server process.

### Web shell

A web shell receives commands through HTTP parameters such as GET or POST and returns output through the web server. Review the difference between an HTTP request/response workflow and an interactive terminal, and common webroot paths across Apache, Nginx, IIS, and XAMPP.

### Terminal interaction (TTY)

A minimal shell may not provide line editing, command history, or correct terminal sizing. Review pseudo-terminals, job control, terminal modes, the `TERM` environment variable, and rows/columns.

## Review checklist

- [ ] Explain who initiates each type of connection.
- [ ] Compare reverse, bind, and web shells.
- [ ] Recognize VPN tunnel interface addresses in a lab.
- [ ] Explain why basic sessions may lack interactive TTY features.
- [ ] Interpret `id`, `ip a`, `echo $TERM`, and `stty size`.
- [ ] Practice and document results in authorized labs.

> The original shared material contains additional command examples. This GitHub topic is a study-oriented reference, not a verbatim archive of that source.
