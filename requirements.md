# Requirements

## Hardware
- 8 GB RAM minimum
- 16 GB RAM recommended
- 40 GB+ free storage
- Modern x86-64 CPU

## Virtualization
- VirtualBox or VMware Workstation

## Recommended Lab VMs
- Kali Linux
- Windows or Linux target
- Optional firewall/IDS test VM

## Network
Use an isolated Host-Only or Internal Network for active scanning/evasion exercises.

## Software by Lab

### Lab 01
- Nmap
- Angry IP Scanner

### Lab 02
- MegaPing
- NetScanTools Pro
- sx
- Nmap
- Hping3

### Lab 03
- Wireshark
- Nmap
- Unicornscan

### Lab 04
- Nmap
- Wireshark
- Colasoft Packet Builder
- Hping3
- Proxy Switcher
- CyberGhost VPN

### Lab 05
- Metasploit Framework / msfconsole

## General Checks

Linux:
```bash
ip addr
ip route
```

Windows:
```powershell
ipconfig
```

Verify that the scanner and target are reachable before beginning.
