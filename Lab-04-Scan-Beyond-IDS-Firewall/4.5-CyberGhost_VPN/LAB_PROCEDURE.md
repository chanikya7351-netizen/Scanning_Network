# Lab Procedure — CyberGhost VPN

## Step 1 — Record the original public IP

```powershell
curl.exe https://api.ipify.org
```

Record:

```text
Original public IP: __________________
```

## Step 2 — Inspect interfaces

```powershell
ipconfig
```

Identify:

- Physical network adapter
- CyberGhost virtual adapter

## Step 3 — Start Wireshark

Select the physical Ethernet/Wi-Fi interface.

Start capture.

## Step 4 — Generate traffic

```powershell
curl.exe https://example.com
```

## Step 5 — Connect CyberGhost

Connect to a VPN server.

## Step 6 — Check public IP again

```powershell
curl.exe https://api.ipify.org
```

Record:

```text
VPN public IP: __________________
```

## Step 7 — Inspect VPN packets

Wireshark:

```wireshark
udp
```

Then narrow:

```wireshark
ip.addr == 192.168.1.10 && udp
```

Look for traffic from the Windows physical IP toward the VPN server.

## Step 8 — Inspect the VPN virtual interface

Use:

```wireshark
ip.addr == 10.6.140.180
```

Replace the address with the actual CyberGhost virtual adapter address.

## Step 9 — DNS observation

```wireshark
dns
```

Also run:

```powershell
nslookup example.com
```

## Step 10 — Optional Kali observation

If Kali is on an observation-capable shared network:

```bash
sudo tcpdump -ni eth0 host 192.168.1.10 and udp
```

Then generate traffic from Windows.

If no traffic appears, check the VirtualBox network topology. Host-only networking alone does not make Kali a network sniffer for Windows' Internet traffic.

---

# Packet Flow

## Without VPN

```text
Windows
192.168.1.10
     |
     v
Router / ISP
     |
     v
Internet
```

## With VPN

```text
Windows
192.168.1.10
     |
     | Encrypted UDP tunnel
     v
CyberGhost VPN Server
     |
     v
Internet
     |
     v
Website
```

---

# Observation Table

| Observation | Result |
|---|---|
| Public IP before VPN | __________________ |
| Public IP after VPN | __________________ |
| VPN protocol | __________________ |
| Physical interface | __________________ |
| VPN virtual interface | __________________ |
| VPN virtual IP | __________________ |
| VPN server IP | __________________ |
| Outer transport | __________________ |
| DNS behavior | __________________ |

---

# Final Explanation

A VPN does not make packets disappear from the network.

Instead:

1. The application creates traffic.
2. The VPN client encrypts/encapsulates it.
3. The encrypted traffic is sent to the VPN server.
4. The VPN server forwards traffic to the Internet.
5. The destination sees the VPN server's public IP.

Wireshark can observe the transport metadata and encrypted packets, but normally cannot read the tunneled application payload without the appropriate cryptographic keys and decryption context.
