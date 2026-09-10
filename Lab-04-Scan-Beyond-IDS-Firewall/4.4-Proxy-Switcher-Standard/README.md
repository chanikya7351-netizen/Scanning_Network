# Lab 04.4 — Browse Anonymously Using Proxy Switcher Standard

## Module 03: Scanning Networks

### Aim

To understand how a proxy acts as an intermediary between a client and a web server using Proxy Switcher Standard and Squid Proxy, and to observe the traffic using Wireshark.

> **Note:** A proxy does not guarantee complete anonymity. This lab demonstrates proxy-based traffic mediation in a controlled lab environment.

---

## Lab Topology

### Without Proxy

```text
Windows Client
192.168.1.10
      |
      | HTTP :8000
      v
Kali Linux
192.168.1.6:8000
Python Web Server
```

### With Proxy

```text
Windows Client
192.168.1.10
      |
      | TCP :3128
      v
Kali Linux
192.168.1.6:3128
Squid Proxy
      |
      | HTTP request
      v
Web Server
Port 8000
```

### Example IP Addresses

| System | IP | Role |
|---|---|---|
| Windows VM | `192.168.1.10` | Client + Proxy Switcher |
| Kali Linux | `192.168.1.6` | Squid + Python server |
| Squid | `192.168.1.6:3128` | Proxy |
| Python server | `192.168.1.6:8000` | Test web server |

> Replace these IPs with your actual lab IP addresses.

---

# 1. Required Tools

## Windows

- Windows VM
- Proxy Switcher Standard
- Web Browser
- Wireshark

## Kali Linux

- Squid
- Python 3
- Wireshark
- curl

---

