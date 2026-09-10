# 2.4: Explore Various Network Scanning Techniques using Nmap

## Objective
Explore common Nmap TCP, UDP and service-discovery techniques in an authorized lab.

## TCP Connect scan
```bash
sudo nmap -sT <TARGET-IP>
```

## SYN scan
```bash
sudo nmap -sS <TARGET-IP>
```

## UDP scan
```bash
sudo nmap -sU --top-ports 20 <TARGET-IP>
```

## Service/version detection
```bash
sudo nmap -sV <TARGET-IP>
```

## OS detection
```bash
sudo nmap -O <TARGET-IP>
```

## Combined lab scan
```bash
sudo nmap -sS -sV -O <TARGET-IP>
```

## Selected ports
```bash
sudo nmap -sS -sV -p 22,80,443 <TARGET-IP>
```

## Compare
| Scan | Purpose |
|---|---|
| `-sT` | TCP connect |
| `-sS` | TCP SYN |
| `-sU` | UDP |
| `-sV` | Service/version detection |
| `-O` | OS detection |

Use only authorized targets.
