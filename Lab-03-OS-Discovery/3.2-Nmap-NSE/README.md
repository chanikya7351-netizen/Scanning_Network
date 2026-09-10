# 3.2: Perform OS Discovery using Nmap Script Engine (NSE)

## Objective
Use Nmap OS detection and NSE scripts to collect additional authorized target information.

## Basic OS detection
```bash
sudo nmap -O <TARGET-IP>
```

## Service detection
```bash
sudo nmap -sV <TARGET-IP>
```

## SMB OS discovery
On an authorized Windows lab target:
```bash
sudo nmap -O -sV --script smb-os-discovery -p 135,139,445 <TARGET-IP>
```

## NSE script information
```bash
nmap --script-help smb-os-discovery
```

## Interpretation
Treat Nmap fingerprints and NSE results as evidence. Version, firewall behavior and service configuration can affect accuracy.

## Troubleshooting
- Ensure the target is reachable.
- For SMB scripts, ports 135/139/445 must be reachable.
- Run with `sudo` for OS fingerprinting.
