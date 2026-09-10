# 2.5: Explore Various Network Scanning Techniques using Hping3

## Objective
Understand packet-level TCP scanning with hping3.

## Verify installation
```bash
hping3 --help
```

## TCP SYN probe
```bash
sudo hping3 -S -p 80 -c 3 <TARGET-IP>
```

## Common ports
```bash
sudo hping3 -S -p 22 -c 3 <TARGET-IP>
sudo hping3 -S -p 80 -c 3 <TARGET-IP>
sudo hping3 -S -p 443 -c 3 <TARGET-IP>
```

A SYN-ACK response generally indicates the port is reachable/open; RST commonly indicates a closed TCP port. Filtering or packet loss can produce no response.

## Small authorized port loop
```bash
for p in 22 80 443 3389; do
  echo "=== Port $p ==="
  sudo hping3 -S -p $p -c 1 <TARGET-IP>
done
```

## UDP example
```bash
sudo hping3 --udp -p 53 -c 3 <TARGET-IP>
```

Interpret packets with Wireshark/tcpdump where appropriate.
