# Angry IP Scanner Usage

## Determine the network

Linux:
```bash
ip addr
ip route
```

Windows:
```powershell
ipconfig
```

## Configure the range
Example:
```text
Start: 192.168.56.1
End:   192.168.56.254
```

Start the scan and record live hosts.

| IP | Hostname | MAC | Response Time | Notes |
|---|---|---|---|---|
| | | | | |
| | | | | |

Compare with:
```bash
sudo nmap -sn 192.168.56.0/24
```
