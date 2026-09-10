# Installation Guide

## 1. CyberGhost VPN — Windows

Official website:

https://www.cyberghostvpn.com/

### Steps

1. Download the Windows application.
2. Install it.
3. Sign in.
4. Open the application.
5. Select a VPN server.
6. Connect.
7. Verify the public IP changed.

Check IP:

```powershell
curl.exe https://api.ipify.org
```

---

## 2. Wireshark — Windows

Official website:

https://www.wireshark.org/download.html

### Steps

1. Download Wireshark for Windows.
2. Start the installer.
3. Install Npcap when prompted.
4. Finish installation.
5. Launch Wireshark.
6. Select the active Ethernet/Wi-Fi interface.
7. Start capture.

---

## 3. Wireshark — Kali Linux

Kali normally includes Wireshark, but install/update it if necessary:

```bash
sudo apt update
sudo apt install wireshark -y
```

Launch:

```bash
wireshark
```

or:

```bash
sudo wireshark
```

For command-line capture:

```bash
sudo tcpdump -ni eth0
```

---

## 4. Useful Windows Commands

Show interfaces:

```powershell
ipconfig
```

Show routing table:

```powershell
route print
```

Show routes:

```powershell
Get-NetRoute
```

Test DNS:

```powershell
nslookup example.com
```

Trace route:

```powershell
tracert 8.8.8.8
```

Check public IP:

```powershell
curl.exe https://api.ipify.org
```

---

## 5. Useful Kali Commands

Show interfaces:

```bash
ip addr
```

Show routes:

```bash
ip route
```

Test Windows:

```bash
ping 192.168.1.10
```

Capture Windows UDP traffic:

```bash
sudo tcpdump -ni eth0 host 192.168.1.10 and udp
```

Capture all traffic involving Windows:

```bash
sudo tcpdump -ni eth0 host 192.168.1.10
```
