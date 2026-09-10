# 1.2: Perform Host Discovery using Angry IP Scanner

## Objective
Discover active hosts with a graphical network scanner.

## Procedure
1. Install and launch Angry IP Scanner.
2. Determine your authorized lab network.
3. Enter the start and end addresses.
4. Start the scan.
5. Identify hosts marked as alive.
6. Record available IP, hostname, MAC and response-time information.
7. Compare the results with Nmap.

Example range:
```text
192.168.56.1 - 192.168.56.254
```

## Comparison
```text
sudo nmap -sn 192.168.56.0/24
```

## Notes
Different tools and configurations may use different discovery mechanisms. Firewall rules and adapter configuration can affect results.

## Safety
Use only authorized networks.
