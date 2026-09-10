# 4.1: Scan Beyond IDS/Firewall using Nmap Evasion Techniques

## Objective
Demonstrate selected Nmap packet/scan options in an isolated authorized lab and observe their effect in Wireshark.

## Baseline
```bash
sudo nmap -sS -p 80,443 <TARGET-IP>
```

## Fragment packets
```bash
sudo nmap -sS -f -p 80 <TARGET-IP>
```

## Custom MTU
Use a valid multiple-of-8 MTU supported by Nmap:
```bash
sudo nmap -sS --mtu 24 -p 80 <TARGET-IP>
```

## Decoy demonstration
Use only lab addresses that you control:
```bash
sudo nmap -sS -D <DECOY-IP>,ME -p 80 <TARGET-IP>
```

## Source-port option
```bash
sudo nmap -sS --source-port 53 -p 80 <TARGET-IP>
```

This changes the source TCP port of probes; it is not a guarantee of firewall bypass.

## Timing
```bash
sudo nmap -sS -T2 -p 80 <TARGET-IP>
sudo nmap -sS -T4 -p 80 <TARGET-IP>
```

## Spoofed source IP
IP spoofing can break return traffic and is unsuitable for ordinary TCP scanning unless the lab is specifically designed for one-way observation. Do not spoof third-party addresses.

## Wireshark observation
Capture on the relevant interface and compare:
- IP identification/fragmentation
- TCP flags
- source port
- packet timing
- number of packets

## Important
These options may trigger IDS alerts rather than evade them. The goal is to understand traffic behavior, not to promise a bypass.
