- Rustscan
  - 基本使用
    ```shell
    rustscan -a <target_ip> -p <port_range> --ulimit 5000
    ```
  - 連動 nmap
    ```shell
    rustscan -a <target_ip> -- <nmap option>
    ```
- Nmap
  - SYN Scan
    ```shell
    sudo nmap -sS <target_ip>
    ```
  - 版本掃描
    ```shell
    nmap -sC -sV -T4 <target_ip>
    ```
  - SSH Credential:
    ```shell
    nmap --script ssh-auth-methods -p22 <target_ip>
    ```