# 3.1: Identify OS using TTL and TCP Window Size with Wireshark

## Objective
Observe packet-level characteristics and understand how TTL and TCP window size can provide OS clues.

## Capture
Start Wireshark on the interface connected to your isolated lab network.

Useful display filters:
```text
icmp
tcp
tcp.flags.syn == 1
ip.addr == <TARGET-IP>
```

## What to inspect
Select a packet and inspect:
- IP header -> Time to Live
- TCP header -> Window Size
- TCP options
- SYN/SYN-ACK characteristics

## TTL concept
TTL limits how many routing hops a packet can traverse before being discarded. Operating systems commonly choose characteristic initial TTL values, but the observed TTL may be lower after routing.

Therefore TTL is an indicator, not proof.

## TCP window concept
The advertised TCP receive window is part of flow control. Its value and TCP options can contribute to fingerprinting, but values can vary due to OS versions, network stacks, scaling and configuration.

## Demonstration
1. Generate traffic from Kali to the target.
    To generate traffic use methods like ping, Host Discovery which was discovered in LAB 1
2. Capture the response.
3. Select the SYN-ACK.
4. Record TTL and window size.
5. Compare observations with Nmap OS detection.

## Important
Do not claim an OS solely from one TTL or window-size value.
