## 偵查

```shell
# Nmap 7.95 scan initiated Thu Sep  4 10:11:25 2025 as: /usr/lib/nmap/nmap --privileged -vvv -p 22,80,8082,9999 -4 -sC -sV -o scan_result.txt 192.168.146.25
Nmap scan report for 192.168.146.25
Host is up, received reset ttl 61 (0.071s latency).
Scanned at 2025-09-04 10:11:26 EDT for 24s

PORT     STATE SERVICE  REASON         VERSION
22/tcp   open  ssh      syn-ack ttl 61 OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 c9:c3:da:15:28:3b:f1:f8:9a:36:df:4d:36:6b:a7:44 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDNEbgprJqVJa8R95Wkbo3cemB4fdRzos+v750LtPEnRs+IJQn5jcg5l89Tx4junU+AXzLflrMVo55gbuKeNTDtFRU9ltlIu4AU+f7lRlUlvAHlNjUbU/z3WBZ5ZU9j7Xc9WKjh1Ov7chC0UnDdyr5EGrIwlLzgk8zrWx364+S4JqLtER2/n0rhVxa9RCw0tR/oL24kMep4q7rFK6dThiRtQ9nsJFhh6yw8Fmdg7r4uohqH70UJurVwVNwFqtr/86e4VSSoITlMQPZrZFVvoSsjyL8LEODt1qznoLWudMD95Eo1YFSPID5VcS0kSElfYigjSr+9bNSdlzAof1mU6xJA67BggGNu6qITWWIJySXcropehnDAt2nv4zaKAUKc/T0ij9wkIBskuXfN88cEmZbu+gObKbLgwQSRQJIpQ+B/mA8CD4AiaTmEwGSWz1dVPp5Fgb6YVy6E4oO9ASuD9Q1JWuRmnn8uiHF/nPLs2LC2+rh3nPLXlV+MG/zUfQCrdrE=
|   256 26:03:2b:f6:da:90:1d:1b:ec:8d:8f:8d:1e:7e:3d:6b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCUhhvrIBs53SApXKZYHWBlpH50KO3POt8Y+WvTvHZ5YgRagAEU5eSnGkrnziCUvDWNShFhLHI7kQv+mx+4R6Wk=
|   256 fb:43:b2:b0:19:2f:d3:f6:bc:aa:60:67:ab:c1:af:37 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIN4MSEXnpONsc0ANUT6rFQPWsoVmRW4hrpSRq++xySM9
80/tcp   open  http     syn-ack ttl 61 nginx 1.18.0
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_http-title: 403 Forbidden
|_http-server-header: nginx/1.18.0
8082/tcp open  http     syn-ack ttl 61 Barracuda Embedded Web Server
|_http-server-header: BarracudaServer.com (Posix)
| http-methods: 
|   Supported Methods: OPTIONS GET HEAD PROPFIND PATCH POST PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK
|_  Potentially risky methods: PROPFIND PATCH PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK
|_http-title: Home
| http-webdav-scan: 
|   Server Type: BarracudaServer.com (Posix)
|   WebDAV type: Unknown
|   Allowed Methods: OPTIONS, GET, HEAD, PROPFIND, PATCH, POST, PUT, COPY, DELETE, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK
|_  Server Date: Thu, 04 Sep 2025 14:11:46 GMT
|_http-favicon: Unknown favicon MD5: FDF624762222B41E2767954032B6F1FF
9999/tcp open  ssl/http syn-ack ttl 61 Barracuda Embedded Web Server
| http-webdav-scan: 
|   Server Type: BarracudaServer.com (Posix)
|   WebDAV type: Unknown
|   Allowed Methods: OPTIONS, GET, HEAD, PROPFIND, PATCH, POST, PUT, COPY, DELETE, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK
|_  Server Date: Thu, 04 Sep 2025 14:11:47 GMT
|_http-server-header: BarracudaServer.com (Posix)
| http-methods: 
|   Supported Methods: OPTIONS GET HEAD PROPFIND PATCH POST PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK
|_  Potentially risky methods: PROPFIND PATCH PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK
| ssl-cert: Subject: commonName=FuguHub/stateOrProvinceName=California/countryName=US
| Subject Alternative Name: DNS:FuguHub, DNS:FuguHub.local, DNS:localhost
| Issuer: commonName=Real Time Logic Root CA/organizationName=Real Time Logic LLC/countryName=US/emailAddress=ginfo@realtimelogic.com/organizationalUnitName=SharkSSL
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2019-07-16T19:15:09
| Not valid after:  2074-04-18T19:15:09
| MD5:   6320:2067:19be:be32:18ce:3a61:e872:cc3f
| SHA-1: 503c:a62d:8a8c:f8c1:6555:ec50:77d1:73cc:0865:ec62
| -----BEGIN CERTIFICATE-----
| MIIEfDCCA2SgAwIBAgIBEDANBgkqhkiG9w0BAQsFADCBiDELMAkGA1UEBhMCVVMx
| HDAaBgNVBAoTE1JlYWwgVGltZSBMb2dpYyBMTEMxETAPBgNVBAsTCFNoYXJrU1NM
| MSAwHgYDVQQDExdSZWFsIFRpbWUgTG9naWMgUm9vdCBDQTEmMCQGCSqGSIb3DQEJ
| ARYXZ2luZm9AcmVhbHRpbWVsb2dpYy5jb20wIBcNMTkwNzE2MTkxNTA5WhgPMjA3
| NDA0MTgxOTE1MDlaMDQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIDApDYWxpZm9ybmlh
| MRAwDgYDVQQDDAdGdWd1SHViMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKC
| AQEA2uaLiecelbCvdPpZcmweQmK+rcxjFwKKx+ButcLc/vgDTbbDDsg9Pfn78RNE
| hrM1WsvkGXKgJY2M9tzTB6o27pUipwfYAA8a4mV7wfM+YGOVuC05t3oh3+GtSciQ
| ZNDEX3cSZ5XanKPNe9vNBNtFiC5ujadbsLWJxC4mHR9fnx3fPlhQO25QBcX+0M1h
| vOwsidZaqyl+R1zMOi/6IpJJobTJMRKT23N61q3ZKeJnZM2JK9H2srC8tRlIidpf
| Iu3Sv7St3Hg2VzbtlEDpGdISZTwpB+MIgvcZ2Z5IVfnhYJlQlNGHbg8jnegA07VE
| AsfkNlyMfVO88TCo5fLhE6wZLwIDAQABo4IBQDCCATwwHQYDVR0OBBYEFI5fqYOD
| ozZher2qQbU3//9gzTIyMIG1BgNVHSMEga0wgaqAFHlYmIP3mqIAiKOI3DxqZo3k
| KQKGoYGOpIGLMIGIMQswCQYDVQQGEwJVUzEcMBoGA1UEChMTUmVhbCBUaW1lIExv
| Z2ljIExMQzERMA8GA1UECxMIU2hhcmtTU0wxIDAeBgNVBAMTF1JlYWwgVGltZSBM
| b2dpYyBSb290IENBMSYwJAYJKoZIhvcNAQkBFhdnaW5mb0ByZWFsdGltZWxvZ2lj
| LmNvbYIBADAJBgNVHRMEAjAAMAsGA1UdDwQEAwIDqDAdBgNVHSUEFjAUBggrBgEF
| BQcDAgYIKwYBBQUHAwEwLAYDVR0RBCUwI4IHRnVndUh1YoINRnVndUh1Yi5sb2Nh
| bIIJbG9jYWxob3N0MA0GCSqGSIb3DQEBCwUAA4IBAQBsn2vzaK/2bKmWJ52FiRVw
| K3NxCqQrxHjXp2nJFc0S05XnCUqN5RbTgyhv0a8ODniaiJbXAaJC34Ot+v6UMeWE
| CHmZgjbZUvsrQiEUz+i0fegtwp+zgSOlb6t+g3lPGTzBjlFUfb0gaRSxSoI42apG
| QBwoN1haaHhm6THC7DIYmFiOgRiQfitiTf9FtbwTTfrLnVm3e3dCuwCmkcDgBZE1
| Q0M4pGSS3vWTQxF6oyYNe7mhtvGeG7qYE6amtF9iuiUt0MrH4hXTMfHcjHdRULgc
| 1a3vVPwl5CZsQRz40OPrQVtF7rcXRNWsU/1ze9r1sgeLxvjWmbW0GByKJ+jnHALy
|_-----END CERTIFICATE-----
|_http-favicon: Unknown favicon MD5: FDF624762222B41E2767954032B6F1FF
|_http-title: Home
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Sep  4 10:11:50 2025 -- 1 IP address (1 host up) scanned in 24.98 seconds

```

## 列舉

看看 CMS (FuguHub-8.4)
找找 [exploit](https://github.com/SanjinDedic/FuguHub-8.4-Authenticated-RCE-CVE-2024-27697/blob/main/exploit.py)  
然後 Get root shell