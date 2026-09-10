# Nmap Host Discovery Command Sheet

```bash
ip addr
ip route

sudo nmap -sn <NETWORK>/24
sudo nmap -sn <TARGET-IP>

sudo nmap -sn -PR <NETWORK>/24
sudo nmap -sn -PE <NETWORK>/24
sudo nmap -sn -PP <NETWORK>/24
sudo nmap -sn -PS80 <NETWORK>/24
sudo nmap -sn -PS22,80,443 <NETWORK>/24
sudo nmap -sn -PA80 <NETWORK>/24
sudo nmap -sn -PU53 <NETWORK>/24
sudo nmap -sn -n <NETWORK>/24
```
