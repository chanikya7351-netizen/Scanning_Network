# 1.1: Perform Host Discovery using Nmap

## Objective
Discover live hosts without performing a full port scan.

## Step 1: Identify the interface and network
```bash
ip addr
ip route
```
<IP_KALI> = IP ADDRESS OF KALI
<IP_TARGET> = IP ADDRESS OF TARGET

## Step 2: Basic host discovery
```bash
sudo nmap -sn <IP_KALI>/24
```
`-sn` performs host discovery and skips the normal port scan.

## Step 3: Single-host discovery
```bash
sudo nmap -sn <TARGET_IP>
```

## Step 4: ARP discovery
```bash
sudo nmap -sn -PR <IP_KALI>/24
```
Useful on the local Ethernet segment.

## Step 5: ICMP Echo
```bash
sudo nmap -sn -PE <IP_KALI>/24
```

## Step 6: ICMP Timestamp
```bash
sudo nmap -sn -PP <IP_KALI>/24
```

## Step 7: TCP SYN discovery
```bash
sudo nmap -sn -PS22,80,443 <IP_KALI>/24
```
HERE  WE MENTION PORT NUMBERS AFTER -PS

## Step 8: TCP ACK discovery
```bash
sudo nmap -sn -PA80 <IP_KALI>/24
```
HERE  WE MENTION PORT NUMBERS AFTER -PA

## Step 9: UDP discovery
```bash
sudo nmap -sn -PU53 <IP_KALI>/24
```
HERE  WE MENTION PORT NUMBERS AFTER -PU

## Step 10: Disable DNS resolution
```bash
sudo nmap -sn -n <IP_KALI>/24
```


## Common options

| Option | Meaning |
|---|---|
| `-sn` | Host discovery only |
| `-PR` | ARP discovery |
| `-PE` | ICMP Echo |
| `-PP` | ICMP Timestamp |
| `-PS` | TCP SYN discovery |
| `-PA` | TCP ACK discovery |
| `-PU` | UDP discovery |
| `-n` | Disable DNS resolution |

## Expected result
Nmap reports hosts that respond to the selected discovery mechanisms. Results can differ between methods because firewalls and host configurations may block particular probes.

## Troubleshooting
- Check `ip route`.
- Confirm both VMs are on the same isolated network.
- Check the target firewall.
- Use `sudo`.
