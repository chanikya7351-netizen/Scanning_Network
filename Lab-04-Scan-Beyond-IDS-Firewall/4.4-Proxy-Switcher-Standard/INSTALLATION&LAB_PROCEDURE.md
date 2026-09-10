# 2. Installation

## 2.1 Proxy Switcher Standard

Official website:

https://www.proxyswitcher.com/

Install **Proxy Switcher Standard** on Windows.

Launch it after installation.

---

## 2.2 Wireshark

Official website:

https://www.wireshark.org/

On Kali:

```bash
sudo apt update
sudo apt install wireshark -y
```

Verify:

```bash
wireshark --version
```

Install Wireshark on Windows using the official installer if required.

---

## 2.3 Squid

On Kali:

```bash
sudo apt update
sudo apt install squid -y
```

Verify:

```bash
squid --version
```

Check service:

```bash
sudo systemctl status squid
```

Start if required:

```bash
sudo systemctl start squid
```

Enable at boot:

```bash
sudo systemctl enable squid
```

---

## 2.4 Python

Check:

```bash
python3 --version
```

If required:

```bash
sudo apt install python3 -y
```

---

# 3. Find Kali IP Address

Run on Kali:

```bash
ip -br addr
```

Example:

```text
eth0    UP    192.168.1.6/24
```

Use the actual Kali IP address throughout the lab.

---

# 4. Test Network Connectivity

From Windows PowerShell:

```powershell
ping 192.168.1.6
```

If ping is blocked, test the proxy port:

```powershell
Test-NetConnection 192.168.1.6 -Port 3128
```

Expected:

```text
TcpTestSucceeded : True
```

If it is `False`, troubleshoot the VM network, Squid, and firewall before continuing.

---

# 5. Configure Squid

## 5.1 Backup Configuration

```bash
sudo cp /etc/squid/squid.conf /etc/squid/squid.conf.backup
```

## 5.2 Edit Configuration

```bash
sudo nano /etc/squid/squid.conf
```

For this controlled lab, use:

```text
http_port 3128

acl localhost src 127.0.0.1/32
acl labnet src 192.168.1.0/24

http_access allow localhost
http_access allow labnet
http_access deny all

access_log /var/log/squid/access.log
```

If your lab uses another subnet, change:

```text
192.168.1.0/24
```

to your lab subnet.

---

# 6. Understand the Squid Configuration

### Proxy port

```text
http_port 3128
```

Squid listens on TCP port `3128`.

### Lab network

```text
acl labnet src 192.168.1.0/24
```

Defines the allowed lab network.

### Allow lab clients

```text
http_access allow labnet
```

Allows clients from the lab network.

### Deny other clients

```text
http_access deny all
```

Denies clients that do not match the allowed rules.

### Access log

```text
access_log /var/log/squid/access.log
```

Records proxy requests.

---

# 7. Validate and Restart Squid

Check the configuration:

```bash
sudo squid -k parse
```

Restart:

```bash
sudo systemctl restart squid
```

Check:

```bash
sudo systemctl status squid
```

Expected:

```text
Active: active (running)
```

---

# 8. Verify Port 3128

Run:

```bash
sudo ss -lntp | grep 3128
```

Expected output is similar to:

```text
LISTEN 0 256 *:3128 *:* users:(("squid",pid=...,fd=...))
```

This confirms that Squid is listening on port `3128`.

---

# 9. Configure Kali Firewall

Check UFW:

```bash
sudo ufw status numbered
```

Allow only TCP/3128 from the lab network:

```bash
sudo ufw allow from 192.168.1.0/24 to any port 3128 proto tcp
```

Reload:

```bash
sudo ufw reload
```

Verify:

```bash
sudo ufw status numbered
```

> Make sure an earlier DENY rule is not blocking the connection.

---

# 10. Test Squid Locally

Run on Kali:

```bash
curl -x http://127.0.0.1:3128 http://example.com -I
```

If you receive `ERR_ACCESS_DENIED`, check that these rules exist:

```text
acl localhost src 127.0.0.1/32
http_access allow localhost
```

Then:

```bash
sudo squid -k parse
sudo systemctl restart squid
```

---

# 11. Create the Test Web Server

Create a directory:

```bash
mkdir -p ~/proxy-lab-web
cd ~/proxy-lab-web
```

Create a test page:

```bash
echo "<h1>Proxy Switcher Lab - Test Web Server</h1>" > index.html
```

Start the server:

```bash
python3 -m http.server 8000 --bind 0.0.0.0
```

Expected:

```text
Serving HTTP on 0.0.0.0 port 8000
```

Keep this terminal running.

---

# 12. Test Without Proxy

From the Windows browser, open:

```text
http://192.168.1.6:8000
```

The test page should appear.

The connection is:

```text
Windows
192.168.1.10
      |
      | TCP :8000
      v
Kali
192.168.1.6:8000
```

This is the **direct connection**.

---

# 13. Wireshark — Capture Direct Traffic

Start Wireshark on Kali and select the lab interface, for example:

```text
eth0
```

Use:

```text
ip.addr == 192.168.1.10 && ip.addr == 192.168.1.6 && tcp.port == 8000
```

Refresh:

```text
http://192.168.1.6:8000
```

You should observe:

```text
Windows                         Web Server
   |                                |
   | -------- SYN ----------------> |
   | <------- SYN, ACK ------------ |
   | -------- ACK ----------------> |
   | -------- GET / --------------> |
   | <------- HTTP response ------- |
```

### Teaching Point

Without a proxy:

```text
Client -----------------> Web Server
       Direct connection
```

---

# 14. Configure Proxy Switcher Standard

Open **Proxy Switcher Standard** on Windows.

Add a proxy:

```text
Proxy Type: HTTP
Proxy Address: 192.168.1.6
Proxy Port: 3128
```

Save and activate/select the proxy.

> The exact user interface can vary between versions.

---

# 15. Test the Proxy Port

From Windows PowerShell:

```powershell
Test-NetConnection 192.168.1.6 -Port 3128
```

Expected:

```text
TcpTestSucceeded : True
```

Do not continue until the TCP connection to port `3128` works.

---

# 16. Wireshark — Capture Proxy Traffic

Use:

```text
ip.addr == 192.168.1.10 && ip.addr == 192.168.1.6 && tcp.port == 3128
```

Generate a new browser request through Proxy Switcher.

You should see:

```text
192.168.1.10 → 192.168.1.6
51085 → 3128
[SYN]
```

Then:

```text
192.168.1.6 → 192.168.1.10
3128 → 51085
[SYN, ACK]
```

Then:

```text
192.168.1.10 → 192.168.1.6
51085 → 3128
[ACK]
```

### Teaching Point

Port `3128` is the Squid proxy port.

Therefore:

```text
Windows → 192.168.1.6:3128
```

shows the client communicating with the proxy.

---

# 17. Verify Squid Access Logs

On Kali:

```bash
sudo tail -f /var/log/squid/access.log
```

Generate a new request from Windows through Proxy Switcher.

A log entry may look similar to:

```text
192.168.1.10 ... TCP_MISS/200 GET http://...
```

The exact output depends on the Squid version and request.

The log confirms that Squid processed the request.

---

# 18. Important Wireshark Filters

### Proxy traffic

```text
tcp.port == 3128
```

### Web server traffic

```text
tcp.port == 8000
```

### Windows ↔ Kali

```text
ip.addr == 192.168.1.10 && ip.addr == 192.168.1.6
```

### Windows → Squid

```text
ip.addr == 192.168.1.10 && ip.addr == 192.168.1.6 && tcp.port == 3128
```

### HTTP

```text
http
```

### TCP SYN

```text
tcp.flags.syn == 1
```

### TCP SYN without ACK

```text
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

---

# 19. Direct vs Proxy Comparison

| Feature | Without Proxy | With Proxy |
|---|---|---|
| Connection | Client → Server | Client → Proxy → Server |
| Important port | `8000` | `3128` |
| Intermediary | No | Squid |
| Wireshark | Client → `:8000` | Client → `:3128` |
| Squid access log | Not involved | Request appears |

---

# 20. Important Observation

If Squid and the Python server are both running on the same Kali machine, Wireshark can show both:

```text
Windows → Kali:3128
```

and:

```text
Windows → Kali:8000
```

in a broad capture.

Therefore, do **not** assume that every packet involving port `8000` was forwarded by Squid.

For reliable verification, use:

```bash
sudo tail -f /var/log/squid/access.log
```

and correlate the new request with the Wireshark capture.

---

# 21. Recommended Workshop Demonstration

## Test A — Without Proxy

1. Disable Proxy Switcher.
2. Start Wireshark.
3. Use:

```text
tcp.port == 8000
```

4. Browse to:

```text
http://192.168.1.6:8000
```

5. Show:

```text
Client → Web Server
```

---

## Test B — With Proxy

1. Enable Proxy Switcher.
2. Configure:

```text
192.168.1.6:3128
```

3. Start a fresh Wireshark capture.
4. Use:

```text
tcp.port == 3128
```

5. Browse again.
6. Show:

```text
Client → Squid Proxy
```

7. On Kali run:

```bash
sudo tail -f /var/log/squid/access.log
```

8. Show the corresponding Squid request.

---

# 22. Troubleshooting

## Squid is not running

```bash
sudo systemctl status squid
sudo systemctl restart squid
```

## Port 3128 is not listening

```bash
sudo ss -lntp | grep 3128
```

Check:

```bash
grep -n "http_port" /etc/squid/squid.conf
```

Expected:

```text
http_port 3128
```

## Windows cannot reach port 3128

```powershell
Test-NetConnection 192.168.1.6 -Port 3128
```

On Kali:

```bash
ip -br addr
sudo ss -lntp | grep 3128
sudo ufw status numbered
```

Also check that the Windows and Kali VMs are connected to the same VirtualBox lab network.

## Squid returns 403

Check:

```bash
sudo nano /etc/squid/squid.conf
```

Ensure:

```text
acl localhost src 127.0.0.1/32
acl labnet src 192.168.1.0/24

http_access allow localhost
http_access allow labnet
http_access deny all
```

Then:

```bash
sudo squid -k parse
sudo systemctl restart squid
```

## Python server is not running

```bash
sudo ss -lntp | grep 8000
```

Start:

```bash
python3 -m http.server 8000 --bind 0.0.0.0
```

## No Squid access-log entry

Run:

```bash
sudo tail -f /var/log/squid/access.log
```

Then generate a new browser request.

Verify that Proxy Switcher is active and configured with:

```text
192.168.1.6:3128
```

---

# 23. Key Terms

### Proxy Server

A server that acts as an intermediary between a client and a destination server.

### Squid

An open-source proxy/cache server used as the proxy in this lab.

### Proxy Switcher

A client-side application used to manage and switch proxy configurations.

### Port 3128

The TCP port used by Squid in this lab.

### Port 8000

The TCP port used by the Python test web server.

### Wireshark

A network protocol analyzer used to capture and inspect packets.

### Access Log

A log containing requests processed by Squid.

---

# 24. Viva Questions

### Q1. What is a proxy?

A proxy is an intermediary between a client and a destination server.

### Q2. What is Squid?

Squid is a proxy/cache server.

### Q3. Which port is Squid using?

```text
3128
```

### Q4. What is Proxy Switcher used for?

It manages and switches proxy configurations.

### Q5. What is the difference between direct and proxied communication?

Direct:

```text
Client → Server
```

Proxied:

```text
Client → Proxy → Server
```

### Q6. Why do we use Wireshark?

To capture and analyze network packets.

### Q7. How do we verify that Squid processed a request?

```bash
sudo tail -f /var/log/squid/access.log
```

### Q8. Does a proxy guarantee complete anonymity?

No. A proxy does not guarantee complete anonymity.

---

# 25. Expected Result

The lab is successful when:

- Squid is installed and running.
- Squid listens on TCP port `3128`.
- The Python web server runs on port `8000`.
- Windows can connect to Squid.
- Direct traffic can be observed.
- Proxy traffic to port `3128` can be observed.
- Squid access logs show the client request.

---

# 26. Cleanup

Stop the Python server:

```text
Ctrl + C
```

Stop Squid if no longer required:

```bash
sudo systemctl stop squid
```

Disable the proxy in Proxy Switcher when the lab is complete.

Restore the browser's normal direct connection.

---

# 27. Ethical Notice

Perform this lab only on systems and networks that you own or have explicit permission to test.

This exercise is intended for controlled cybersecurity education.

Do not use proxy infrastructure to bypass authorization, conceal unauthorized activity, or access systems without permission.

---

# 28. References

- Proxy Switcher: https://www.proxyswitcher.com/
- Squid: https://www.squid-cache.org/
- Wireshark: https://www.wireshark.org/
- Python: https://www.python.org/

---

## Lab Completed

The main concept demonstrated is:

```text
WITHOUT PROXY

Client ───────────────────────> Server


WITH PROXY

Client ─────────> Proxy ──────> Server
                 :3128
```

The proxy acts as an intermediary between the client and the destination.
