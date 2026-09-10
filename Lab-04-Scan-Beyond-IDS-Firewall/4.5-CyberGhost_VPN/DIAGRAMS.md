# Lab Diagrams

## Normal Internet Connection

```text
+----------------+
| Windows Client |
| 192.168.1.10   |
+-------+--------+
        |
        v
+----------------+
| Router / ISP   |
+-------+--------+
        |
        v
+----------------+
|    Internet    |
+----------------+
```

## VPN Connection

```text
+----------------+
| Windows Client |
| 192.168.1.10   |
+-------+--------+
        |
        | Encrypted VPN tunnel
        | UDP / VPN protocol
        v
+----------------+
| CyberGhost VPN |
|     Server     |
+-------+--------+
        |
        v
+----------------+
|    Internet    |
+-------+--------+
```

## Wireshark Physical Interface View

```text
Windows
192.168.1.10
     |
     | Outer encrypted transport
     | UDP
     v
VPN_SERVER_IP
```

Wireshark can observe this outer communication.

## Application View

```text
Browser
   |
   v
VPN Virtual Interface
   |
   v
Encrypted Tunnel
   |
   v
VPN Server
   |
   v
Website
```

---

# Kali Observer Concept

```text
             +----------------+
             |  CyberGhost    |
             |   VPN Server   |
             +-------^--------+
                     |
                VPN tunnel
                     |
+---------+     +----+----+
| Windows |-----| Network |
| Client  |     | Segment |
+---------+     +----+----+
                      |
                      |
                 +----v----+
                 |  Kali   |
                 |Wireshark|
                 +---------+
```

Kali must actually be positioned on a network path/observation point where the Windows traffic is visible. Merely adding a Host-Only adapter is not sufficient.
