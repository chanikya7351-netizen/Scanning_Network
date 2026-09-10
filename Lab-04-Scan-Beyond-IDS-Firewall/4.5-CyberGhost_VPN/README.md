# Lab 4.5: Browse Anonymously using CyberGhost VPN

## Objective
Demonstrate how a VPN changes the public IP address and creates an encrypted VPN tunnel. Observe the VPN transport traffic using Wireshark.

> **Important:** A VPN improves privacy and hides the user's public IP from destination websites, but it does not provide complete anonymity.

## Lab Environment

| Component | Example |
|---|---|
| VPN Client | CyberGhost VPN |
| Protocol | WireGuard / OpenVPN / IKEv2 |
| OS | Windows |
| Packet Analyzer | Wireshark |
| Optional Observer | Kali Linux |
| Windows LAN IP | `192.168.1.10` |
| CyberGhost virtual IP | Example: `10.6.140.180` |

IP addresses can differ in your environment.

---

## 1. Install CyberGhost VPN

Download CyberGhost from the official website:

https://www.cyberghostvpn.com/

Install the Windows application and sign in.

### Recommended privacy settings

Enable, where available:

- VPN connection
- Automatic Kill Switch
- DNS leak protection / Prevent DNS Leaks

For a controlled workshop, do not enable random/automatic protocol changes if you want a predictable packet demonstration. Select WireGuard when available.

---

## 2. Install Wireshark

Download Wireshark:

https://www.wireshark.org/download.html

Install it with Npcap enabled.

Open Wireshark as Administrator if required.

---

# 3. Demonstration: Public IP Before VPN

Open PowerShell or Command Prompt:

```powershell
curl.exe https://api.ipify.org
```

or open:

```text
https://whatismyipaddress.com/
```

Record the public IP.

Example:

```text
Before VPN:
Public IP = <ISP_PUBLIC_IP>
```

---

# 4. Connect CyberGhost VPN

1. Open CyberGhost VPN.
2. Select a VPN server/location.
3. Connect.
4. Wait until the VPN reports that it is connected.

Check the IP again:

```powershell
curl.exe https://api.ipify.org
```

The public IP should normally change to the VPN server's public IP.

Example:

```text
Before VPN: <ISP_PUBLIC_IP>
After VPN : <VPN_PUBLIC_IP>
```

---

# 5. Check Windows Network Interfaces

Run:

```powershell
ipconfig
```

Look for:

### Normal network adapter

Example:

```text
Ethernet
IPv4 Address : 192.168.1.10
Default Gateway : 192.168.1.1
```

### CyberGhost virtual adapter

A WireGuard connection may show a virtual interface such as:

```text
CyberGhost-WireGuard-4
IPv4 Address : 10.6.140.180
```

The exact adapter name/address can differ.

---

# 6. Check the Routing Table

Run:

```powershell
route print
```

or:

```powershell
Get-NetRoute
```

Explain that the VPN changes routing so Internet traffic is sent through the VPN tunnel.

You can also run:

```powershell
tracert 8.8.8.8
```

The route may look different when the VPN is connected.

---

# 7. Capture VPN Traffic in Wireshark on Windows

This is the most reliable way to demonstrate CyberGhost VPN traffic.

## Step 1: Start Wireshark

Open Wireshark.

## Step 2: Select the physical network interface

Select the interface carrying the normal network connection, for example:

```text
Ethernet
```

with:

```text
192.168.1.10
```

## Step 3: Start capture

Click **Start**.

## Step 4: Generate traffic

Open a browser and visit:

```text
https://example.com
```

or run:

```powershell
curl.exe https://example.com
```

## Step 5: Apply a filter

For WireGuard, start with:

```wireshark
udp
```

You can narrow it to the Windows LAN address:

```wireshark
ip.addr == 192.168.1.10 && udp
```

You may observe UDP traffic from the Windows machine toward the VPN endpoint.

Example concept:

```text
192.168.1.10:<random_port>
        |
        | UDP encrypted VPN transport
        v
<VPN_SERVER_IP>:<VPN_PORT>
```

### What Wireshark can see

Wireshark can see:

- Source IP of the physical interface
- Destination VPN server IP
- UDP packets
- Packet sizes
- Timing
- Encrypted payload

### What Wireshark normally cannot see

When capturing the physical interface, the application data inside the VPN tunnel is encrypted.

Do **not** say:

> "Wireshark cannot see the VPN."

Correct explanation:

> "Wireshark can see the VPN transport packets, but the tunneled application payload is encrypted."

---

# 8. Capture on the CyberGhost Virtual Interface

You can also inspect the virtual VPN interface.

In Wireshark, identify the CyberGhost/WireGuard virtual adapter.

A filter such as:

```wireshark
ip.addr == 10.6.140.180
```

may show traffic associated with the virtual VPN interface.

The exact packets depend on CyberGhost's implementation and selected protocol.

---

# 9. Understand the Two Views

## Physical Ethernet capture

Conceptually:

```text
Windows Application
       |
       v
VPN tunnel encryption
       |
       v
Ethernet
192.168.1.10
       |
       | UDP
       v
CyberGhost VPN Server
```

Wireshark sees the outer transport:

```text
192.168.1.10  --->  VPN_SERVER_IP
                  UDP
                  encrypted data
```

## VPN virtual interface

Conceptually:

```text
Application
    |
    v
10.6.140.180
    |
    v
VPN tunnel
    |
    v
Encrypted outer packets
    |
    v
VPN server
```

---

# 10. Can Kali Linux See the VPN Packets?

Yes, but **only if the network topology allows Kali to observe the Windows traffic**.

Simply putting Kali and Windows on the same VirtualBox Host-Only network does NOT automatically make Kali see Windows' Internet/VPN packets.

## Example

Windows:

```text
192.168.1.10
```

Kali:

```text
192.168.1.6
```

If Kali is positioned on a network where it can observe the Windows traffic, try:

```bash
sudo tcpdump -ni eth0 host 192.168.1.10
```

Then browse from Windows.

For Wireshark on Kali:

```wireshark
ip.addr == 192.168.1.10 && udp
```

### Important

If CyberGhost uses WireGuard and Windows has:

```text
CyberGhost-WireGuard-4
10.6.140.180
```

do not expect Kali's LAN capture to show:

```text
10.6.140.180 ---> VPN_SERVER
```

The outer packet normally uses the physical network address:

```text
192.168.1.10 ---> VPN_SERVER_IP
```

The `10.6.140.180` address belongs to the virtual VPN interface.

---

# 11. VirtualBox Notes for Kali Observation

### NAT

If Windows uses VirtualBox NAT, Kali normally cannot simply sniff the Windows VM's Internet traffic.

```text
Windows VM
   |
 VirtualBox NAT
   |
 Host
   |
 Internet
```

### Host-Only

Host-only is useful for VM-to-VM communication:

```text
Windows <----> Kali
```

but it does not automatically carry Windows' Internet traffic.

### Bridged / shared network

A shared L2 network can allow Kali and Windows to communicate, but ordinary switched networking does not guarantee that Kali receives another machine's unicast packets.

For a reliable packet-observation lab, use a deliberate topology where traffic passes through Kali or another appropriate observation point.

---

# 12. DNS Leak Demonstration

Before connecting the VPN:

```powershell
nslookup example.com
```

After connecting:

```powershell
nslookup example.com
```

You can observe DNS traffic in Wireshark with:

```wireshark
dns
```

If DNS is transported inside the VPN, the physical-interface capture may not show the normal DNS query to the local/ISP DNS server.

Avoid claiming that one DNS observation alone proves complete DNS-leak protection. Use CyberGhost's own DNS leak protection/status and an external DNS leak test for a stronger demonstration.

---

# 13. Kill Switch Demonstration

The kill switch is designed to prevent Internet traffic from continuing outside the VPN when the VPN connection drops.

### Demonstration

1. Connect CyberGhost.
2. Confirm Internet access.
3. Confirm the public IP.
4. Enable Automatic Kill Switch.
5. Disconnect/interruption-test the VPN according to the application controls.
6. Observe whether Internet access is blocked until the VPN is restored.

Do this only in the controlled lab.

---

# 14. Useful Wireshark Filters

### All UDP

```wireshark
udp
```

### Windows traffic

```wireshark
ip.addr == 192.168.1.10
```

### Windows UDP traffic

```wireshark
ip.addr == 192.168.1.10 && udp
```

### VPN virtual interface IP

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

---

# 15. What to Explain During the Workshop

### Without VPN

```text
Windows
   |
   v
ISP / Router
   |
   v
Internet
```

The destination website can see the user's ISP-assigned public IP.

### With VPN

```text
Windows
   |
   | Encrypted VPN tunnel
   v
CyberGhost VPN Server
   |
   v
Internet
   |
   v
Website
```

The website normally sees the VPN server's public IP instead of the user's normal public IP.

---

# 16. Key Terms

## VPN
Virtual Private Network. Creates a protected tunnel between the client and VPN server.

## VPN Tunnel
Logical communication path through which traffic is encapsulated and protected.

## WireGuard
A modern VPN protocol that commonly uses UDP and cryptographic keys to protect tunnel traffic.

## VPN Server
The remote server that terminates the VPN tunnel and forwards Internet traffic.

## Public IP
The Internet-facing IP address visible to external services.

## Virtual Network Adapter
A software-created network interface used by VPN software to send/receive tunnel traffic.

## Encrypted Payload
Data that has been transformed using cryptography so that observers cannot normally read the original application content.

## Kill Switch
A VPN feature intended to block traffic if the VPN connection is unavailable.

## DNS Leak
A situation where DNS requests escape the intended VPN path and are handled by another DNS resolver/path.

---

# 17. Expected Results

| Test | VPN Disconnected | VPN Connected |
|---|---|---|
| Public IP | ISP IP | VPN server IP |
| VPN virtual adapter | May be inactive | Active |
| VPN tunnel | Not established | Established |
| Outer VPN packets | Not present | Present |
| Application payload on outer interface | Normal network traffic | Encrypted VPN payload |
| Website source IP | ISP public IP | VPN public IP |

Exact packet details depend on the VPN protocol and network configuration.

---

# 18. Workshop Conclusion

CyberGhost VPN creates a protected tunnel between the client and the VPN server. When connected, Internet traffic is routed through that tunnel and the destination website normally sees the VPN server's public IP.

Wireshark can still observe the **outer VPN transport packets**, such as UDP packets used by WireGuard, but the tunneled application payload is encrypted.

The key takeaway is:

```text
VPN provides privacy and traffic protection,
but VPN does not mean complete anonymity.
```

## Safety / Lab Scope

Perform these demonstrations only on systems and networks you own or have permission to test. Do not attempt to bypass security controls on third-party networks.
