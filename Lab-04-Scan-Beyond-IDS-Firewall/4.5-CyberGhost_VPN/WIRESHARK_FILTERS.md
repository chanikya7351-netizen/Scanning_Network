# Wireshark Filters — CyberGhost VPN

## Basic filters

### All UDP

```wireshark
udp
```

### All traffic from Windows

```wireshark
ip.addr == 192.168.1.10
```

### Windows UDP

```wireshark
ip.addr == 192.168.1.10 && udp
```

### VPN virtual IP

```wireshark
ip.addr == 10.6.140.180
```

### DNS

```wireshark
dns
```

### ICMP

```wireshark
icmp
```

### TCP

```wireshark
tcp
```

### HTTP

```wireshark
http
```

### TLS

```wireshark
tls
```

> Replace example IP addresses with the addresses shown by `ipconfig`.

---

# What to Look For

When CyberGhost is connected using WireGuard, the physical interface may show:

```text
Windows LAN IP
      |
      | UDP
      v
VPN server IP
```

The packet payload will appear encrypted rather than as readable application data.

Do not assume every CyberGhost connection will use exactly the same packet pattern. The selected VPN protocol and application version affect the capture.
