# 4.3: Create Custom UDP and TCP Packets using Hping3

## Objective
Generate controlled TCP/UDP packets and inspect their responses.

## TCP SYN
```bash
sudo hping3 -S -p 8000 -c 3 <TARGET-IP>
```

## TCP ACK
```bash
sudo hping3 -A -p 8000 -c 3 <TARGET-IP>
```

## TCP RST
```bash
sudo hping3 -R -p 8000 -c 3 <TARGET-IP>
```

## UDP
```bash
sudo hping3 --udp -p 8000 -c 3 <TARGET-IP>
```

## Source port
```bash
sudo hping3 -S -s 5000 -p 8000 -c 3 <TARGET-IP>
```

## Capture
Wireshark:
```text
tcp.port == 8000
```

tcpdump:
```bash
sudo tcpdump -ni any port 8000
```

## Interpretation
- SYN -> request to start TCP connection.
- SYN-ACK -> service/path responded positively to a SYN.
- RST -> connection reset; often indicates a closed/unexpected TCP state.
- No response -> may indicate filtering, loss, invalid packet, or an unavailable service.

Do not use forged third-party source addresses.
