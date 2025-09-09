## 偵查

```shell
# Nmap 7.95 scan initiated Mon Sep  8 21:22:19 2025 as: /usr/lib/nmap/nmap --privileged -vvv -p 22,80 -4 -sC -sV -o scan_result.txt 192.168.121.38
Nmap scan report for 192.168.121.38
Host is up, received echo-reply ttl 61 (0.079s latency).
Scanned at 2025-09-08 21:22:19 EDT for 10s

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 61 OpenSSH 8.4p1 Debian 5+deb11u2 (protocol 2.0)
| ssh-hostkey: 
|   3072 c9:c3:da:15:28:3b:f1:f8:9a:36:df:4d:36:6b:a7:44 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDNEbgprJqVJa8R95Wkbo3cemB4fdRzos+v750LtPEnRs+IJQn5jcg5l89Tx4junU+AXzLflrMVo55gbuKeNTDtFRU9ltlIu4AU+f7lRlUlvAHlNjUbU/z3WBZ5ZU9j7Xc9WKjh1Ov7chC0UnDdyr5EGrIwlLzgk8zrWx364+S4JqLtER2/n0rhVxa9RCw0tR/oL24kMep4q7rFK6dThiRtQ9nsJFhh6yw8Fmdg7r4uohqH70UJurVwVNwFqtr/86e4VSSoITlMQPZrZFVvoSsjyL8LEODt1qznoLWudMD95Eo1YFSPID5VcS0kSElfYigjSr+9bNSdlzAof1mU6xJA67BggGNu6qITWWIJySXcropehnDAt2nv4zaKAUKc/T0ij9wkIBskuXfN88cEmZbu+gObKbLgwQSRQJIpQ+B/mA8CD4AiaTmEwGSWz1dVPp5Fgb6YVy6E4oO9ASuD9Q1JWuRmnn8uiHF/nPLs2LC2+rh3nPLXlV+MG/zUfQCrdrE=
|   256 26:03:2b:f6:da:90:1d:1b:ec:8d:8f:8d:1e:7e:3d:6b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCUhhvrIBs53SApXKZYHWBlpH50KO3POt8Y+WvTvHZ5YgRagAEU5eSnGkrnziCUvDWNShFhLHI7kQv+mx+4R6Wk=
|   256 fb:43:b2:b0:19:2f:d3:f6:bc:aa:60:67:ab:c1:af:37 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIN4MSEXnpONsc0ANUT6rFQPWsoVmRW4hrpSRq++xySM9
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.56 ((Debian))
|_http-server-header: Apache/2.4.56 (Debian)
|_http-favicon: Unknown favicon MD5: D41D8CD98F00B204E9800998ECF8427E
| http-methods: 
|_  Supported Methods: GET HEAD OPTIONS
|_http-title: W3.CSS Template
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Sep  8 21:22:29 2025 -- 1 IP address (1 host up) scanned in 9.88 seconds
```

## 列舉

- 80 port
  - w3 css
  - Laravel 8.4.0 
可以從以上的攻擊面去找找 exploit  
結果在 Laravel 8.4.0 中找到了 RCE 漏洞 [exploit](https://github.com/joshuavanderpoll/CVE-2021-3129)

  - dirsearch
    ```
    [03:16:30] Starting: 
    [03:16:32] 301 -  313B  - /js  ->  http://192.168.121.38/js/                
    [03:16:36] 403 -  279B  - /.htaccess.orig                                   
    [03:16:36] 403 -  279B  - /.ht_wsr.txt                                      
    [03:16:36] 403 -  279B  - /.htaccess.bak1                                   
    [03:16:36] 403 -  279B  - /.htaccess.sample                                 
    [03:16:36] 403 -  279B  - /.htaccess.save
    [03:16:36] 403 -  279B  - /.htpasswd_test                                   
    [03:16:36] 403 -  279B  - /.htpasswds
    [03:16:36] 403 -  279B  - /.httr-oauth
    [03:16:36] 403 -  279B  - /.htaccess_extra                                  
    [03:16:36] 403 -  279B  - /.htaccess_orig
    [03:16:36] 403 -  279B  - /.htaccess_sc
    [03:16:36] 403 -  279B  - /.htaccessOLD
    [03:16:36] 403 -  279B  - /.htaccessBAK
    [03:16:36] 403 -  279B  - /.htaccessOLD2                                    
    [03:16:36] 403 -  279B  - /.htm                                             
    [03:16:36] 403 -  279B  - /.html
    [03:16:38] 403 -  279B  - /.php                                             
    [03:16:43] 405 -  835B  - /_ignition/execute-solution                       
    [03:17:08] 301 -  314B  - /css  ->  http://192.168.121.38/css/              
    [03:17:12] 200 -    0B  - /favicon.ico                                      
    [03:17:15] 302 -  354B  - /home  ->  http://192.168.121.38/login            
    [03:17:16] 301 -  317B  - /images  ->  http://192.168.121.38/images/        
    [03:17:16] 403 -  279B  - /images/                                          
    [03:17:18] 301 -  321B  - /javascript  ->  http://192.168.121.38/javascript/
    [03:17:18] 404 -  276B  - /javascript/editors/fckeditor
    [03:17:18] 404 -  276B  - /javascript/tiny_mce
    [03:17:18] 403 -  279B  - /js/                                              
    [03:17:20] 200 -    5KB - /login                                            
    [03:17:21] 405 -  835B  - /logout                                           
    [03:17:31] 200 -    5KB - /register                                         
    [03:17:32] 200 -   24B  - /robots.txt                                       
    [03:17:33] 403 -  279B  - /server-status                                    
    [03:17:33] 403 -  279B  - /server-status/                                   
    [03:17:43] 200 -    1KB - /web.config                                       
    ```
    先進入 /login 註冊帳號後，把 DEBUG_MODE 打開
    之後就可以運行 exploit 來 GET rev shell
    
## 提權

先切換到普通使用者，用 offsec 給我們的 credential `skunk/OffSecSkunk0401`  
之後就可以 sudo 直接 `su -` 切換到 root